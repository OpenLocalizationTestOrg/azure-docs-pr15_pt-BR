<properties 
    pageTitle="Baixe o SDK do Azure para Java" 
    description="Aprenda a baixar o SDK do Azure para Java, com código de exemplo fornecido para projetos de Maven e etapas de instalação básico para a Tookit do Azure para Eclipse." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="multiple" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="download-the-azure-sdk-for-java"></a>Baixe o SDK do Azure para Java

Este artigo contém instruções para baixar e instalar as bibliotecas do Azure para Java.

**Observação:** As bibliotecas do Azure para Java são distribuídas sob a [Licença Apache, versão 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Azure bibliotecas para Java - Download Manual

Para instalar manualmente as bibliotecas do Azure para Java, clique em <http://go.microsoft.com/fwlink/?LinkId=690320> para baixar um arquivo ZIP que contém todas as bibliotecas e todas as dependências.

Depois que você baixou o arquivo zip no seu computador, extraia o conteúdo e use uma das opções a seguir para adicionar os arquivos JAR ao seu projeto:

* Importe os arquivos JAR para seu projeto de Java no Eclipse.

* Configure o **Caminho construir** para o seu projeto de Java no Eclipse para incluir o caminho para os arquivos JAR.

Para obter informações detalhadas sobre como configurar caminhos de compilação no Eclipse, consulte o artigo [Java construir caminho] no site do Eclipse.

**Observação:** Consulte o License e ThirdPartyNotices.txt arquivo o zip para licença e outras informações.

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure bibliotecas para Java - construindo com Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Etapa 1: configurar o seu projeto para usar Maven para construir

Criar projetos Maven no Eclipse que usa as bibliotecas Azure Java, seguindo as instruções no [Guia de Introdução com bibliotecas de gerenciamento do Azure para Java] [ maven-getting-started] artigo. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Etapa 2 - configurar suas configurações de Maven com as dependências necessárias

Quando o projeto tiver sido configurado para usar Maven para construir, você pode adicionar o as dependências necessárias para o seu arquivo de pom.xml usando a sintaxe, como o exemplo a seguir. Observe que você não precisa adicionar cada dependência que está listada no exemplo a seguir; Você precisa adicionar as dependências específicas que requer o seu projeto.

> [AZURE.NOTE] Dentro de cada `<version>` elemento no exemplo a seguir, substitua os espaços reservados de "n.n.n" neste exemplo com números de versão válida, que podem ser obtidos do [Repositório de bibliotecas do Azure em Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalando o Kit de ferramentas do Azure para Eclipse

Esta seção contém instruções básicas para instalar o Kit de ferramentas do Azure para Eclipse; Para obter instruções detalhadas, consulte [Instalando o Kit de ferramentas do Azure para Eclipse].

### <a name="prerequisites"></a>Pré-requisitos

1. Sistemas de operting do Windows listados no artigo [o que há de novo no Kit de ferramentas do Azure para Eclipse] .
1. Sistemas Macintosh ou Linux operting listados no artigo [o que há de novo no Kit de ferramentas do Azure para Eclipse] .
1. Eclipse IDE para Java EE desenvolvedores, Indigo ou posterior. Isso pode ser baixado do <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Etapas básicas de instalação

1. No Eclipse, no menu **Ajuda** , selecione **Instalar novo Software**.
1. Insira o local do site <http://dl.microsoft.com/eclipse> e pressione **Enter**.
1. Selecione os itens a ser instalado e clique em **Concluir**.

O Kit de ferramentas do Azure para Eclipse utiliza a versão mais recente do SDK do Azure. Isso pode ser baixado usando o Web Platform Installer (WebPI) em <http://go.microsoft.com/fwlink/?LinkID=252838>. Entretanto, se você não tiver instalado, quando você cria seu primeiro projeto de implantação do Azure, o Kit de ferramentas do Azure para Eclipse será automaticamente instalar a versão apropriada do SDK do Azure.

## <a name="see-also"></a>Consulte também

[Kit de ferramentas de Azure para Eclipse]

[Instalando o Kit de ferramentas do Azure para Eclipse] 

[Criando um aplicativo Hello World para Azure no Eclipse]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Repositório de bibliotecas Azure em Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Caminho de criação de Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novidades no Azure Kit de ferramentas para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333
