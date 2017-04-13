<properties 
    pageTitle="Usar ReportViewer em um Site da Web | Microsoft Azure"
    description="Este tópico descreve como criar um site do Microsoft Azure com o controle de ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina Virtual Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usar ReportViewer em um Site hospedado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Você pode criar um site do Microsoft Azure com o controle de ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina Virtual Microsoft Azure. O controle ReportViewer é em um aplicativo Web que você cria usando o modelo de aplicativo da Web do ASP.NET.

>[AZURE.IMPORTANT] Os modelos de aplicativo Web do ASP.NET MVC não suportam o controle ReportViewer.

Para incorporar ReportViewer em seu site do Microsoft Azure, é necessário concluir as seguintes tarefas.

- **Adicionar** Conjuntos para o pacote de implantação

- **Configurar** Autenticação e autorização

- **Publicar** o aplicativo da Web do ASP.NET no Azure

## <a name="prerequisites"></a>Pré-requisitos

Revise a seção "recomendação geral e práticas recomendadas" em [SQL Server Business Intelligence em máquinas virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Controles ReportViewer são fornecidos com o Visual Studio, Standard Edition ou acima. Se você estiver usando o Web Developer Express Edition, você deve instalar o [MICROSOFT relatório visualizador 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar os recursos de tempo de execução de ReportViewer.
>
>ReportViewer configurado no modo de processamento de local não é suportada no Microsoft Azure.

Examine o white paper [controle de Visualizador de relatórios do Reporting Services e de máquina virtual do Windows Azure com base em servidores de relatórios](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Adicionando conjuntos para o pacote de implantação

Quando você hospedar seu ASP.NET aplicativo no local, os conjuntos de ReportViewer geralmente são instalados diretamente no cache de assembly global (GAC) do servidor IIS durante a instalação do Visual Studio e podem ser acessados diretamente pelo aplicativo. No entanto, quando você hospedar seu aplicativo ASP.NET na nuvem, Microsoft Azure não permitir que nada esteja instalada no GAC, portanto você deve certificar-se dos que conjuntos de ReportViewer estão disponíveis localmente para seu aplicativo. Você pode fazer isso adicionando referências a eles em seu projeto e configurá-los a ser copiado localmente.

No modo de processamento remoto, o controle ReportViewer usa os seguintes conjuntos:

- **Microsoft.ReportViewer.WebForms.dll**: contém o código de ReportViewer, que você precisa usar ReportViewer em sua página. Uma referência para este assembly é adicionada ao seu projeto quando você solta um controle de ReportViewer em uma página ASP.NET no seu projeto.

- **Microsoft.ReportViewer.Common.dll**: contém classes usadas pelo controle ReportViewer em tempo de execução. Ele não é automaticamente adicionado ao seu projeto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para adicionar uma referência a Microsoft.ReportViewer.Common

- O nó do projeto **referências** de atalho e selecione **Adicionar referência**, selecione o conjunto na guia .NET e clique em **Okey**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para disponibilizar os conjuntos localmente pelo seu aplicativo ASP.NET

1. Na pasta **referências** , clique no assembly Microsoft.ReportViewer.Common para que suas propriedades sejam exibidas no painel Propriedades.

1. No painel Propriedades, defina **Cópia Local** para True.

1. Repita as etapas 1 e 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obter o pacote de idiomas do ReportViewer

1. Instale o pacote redistribuível do Microsoft relatório visualizador 2012 Runtime apropriado do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Selecione o idioma na lista suspensa e a página será redirecionada para a página de centro de download correspondente.

1. Clique em **Baixar** para iniciar o download de ReportViewerLP.exe.

1. Depois de baixar ReportViewerLP.exe, clique em **Executar** para instalar imediatamente, ou clique em **Salvar** para salvá-la ao seu computador. Se você clicar em **Salvar**, lembre-se o nome da pasta onde você salvar o arquivo.

1. Localize a pasta onde você salvou o arquivo. ReportViewerLP.exe de atalho, clique em **Executar como administrador**e, em seguida, clique em **Sim**.

1. Após a execução ReportViewerLP.exe, você verá o c:\WINDOWS\Assembly. tem o recurso arquivos **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar para o controle de ReportViewer localizado

1. Baixe e instale o pacote redistribuível do Microsoft relatório visualizador 2012 Runtime seguindo as instruções especificadas acima.

1. Criar <language> pasta no project e copiar o conjunto de recursos associados arquivos lá. Os arquivos de assembly de recursos sejam copiados são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os arquivos de conjunto de recursos e, no painel Propriedades, defina **Copiar para diretório de saída** para "**Sempre copiar**".

1. Defina a cultura & UICulture para o projeto da web. Para obter mais informações sobre como definir a cultura e cultura de interface do usuário para uma página da Web do ASP.NET, consulte [como: definir a cultura e cultura de interface do usuário para globalização de página da Web do ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurar autenticação e autorização

O ReportViewer precisa usar credenciais adequadas para autenticar com o servidor de relatório e as credenciais devem ser autorizadas pelo servidor de relatório para acessar os relatórios que desejar. Para obter informações sobre autenticação, consulte o white paper [controle de Visualizador de relatórios do Reporting Services e de máquina virtual do Windows Azure com base em servidores de relatórios](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar o aplicativo da Web do ASP.NET no Azure

Para obter instruções sobre como publicar um aplicativo Web do ASP.NET no Azure, consulte [como: migrar e publicar um aplicativo Web para o Azure do Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [começar com aplicativos Web e ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Se o comando Adicionar projeto de implantação do Azure ou Adicionar projeto de serviço de nuvem do Azure não aparecer no menu de atalho no Solution Explorer, talvez você precise alterar a estrutura de destino para o projeto para .NET Framework 4.
>
>Os dois comandos fornecem essencialmente a mesma funcionalidade. Um ou o outro comando aparecerão no menu de atalho dependendo de qual versão do Microsoft Azure SDK você instalou.

## <a name="resources"></a>Recursos

[Relatórios da Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence do SQL Server no Azure máquinas virtuais](virtual-machines-windows-classic-ps-sql-bi.md)

[Usar o PowerShell para criar uma máquina virtual Azure com um servidor de relatório do modo nativo](virtual-machines-windows-classic-ps-sql-report.md)

[Relatórios de controle do Visualizador de relatórios de serviços e Microsoft Azure máquina virtual com base em servidores de relatórios](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
