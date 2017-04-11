<properties
    pageTitle="Novidades no Azure Kit de ferramentas para IntelliJ | Microsoft Azure"
    description="Saiba mais sobre os recursos mais recentes no Kit de ferramentas do Azure para IntelliJ."
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
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Novidades no Azure Kit de ferramentas para IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Kit de ferramentas de Azure para lançamentos de IntelliJ

Este artigo contém informações sobre as diversas versões e atualizações mais recentes para o Kit de ferramentas do Azure para IntelliJ.

> [AZURE.NOTE] Há também um kit de ferramentas do Azure para o Eclipse IDE. Para obter mais informações, consulte [Kit de ferramentas do Azure para Eclipse].

### <a name="august-26-2016"></a>26 de agosto de 2016

O Kit de ferramentas do Azure para IntelliJ - lançamento de agosto de 2016 inclui as seguintes melhorias:

* **Distribuições JDK personalizado**. O Kit de ferramentas do Azure para IntelliJ agora suporta especificando e implantar uma versão JDK aleatório para seu contêiner Web App Azure:
  - Além dos JDKs fornecidos pelo Azure, você também pode escolher entre uma ampla seleção de versões de Zulu OpenJDK disponibilizados no Azure por sistemas de Azul.
  - Você também pode especificar sua próprias distribuição JDK se você carregá-lo como um arquivo ZIP à sua conta de armazenamento.
* **Aprimoramentos para o modo de exibição do Azure Explorer**:
  - Suporte para o gerenciamento de máquinas virtuais usando o novo modelo de Gerenciador de recursos do Azure: você pode listar, criar e excluir máquinas virtuais baseados em Gerenciador de recursos sem sair do IDE.
  - Suporte para gerenciamento de contas de armazenamento de blob usando o Gerenciador de recursos do Azure, que completa a funcionalidade existente para gerenciar contas de armazenamento "clássico".
* **O Driver de Microsoft JDBC 6.0 para o SQL Server**. Essa atualização inclui o driver JDBC mais recente para o Microsoft SQL Server (v 6.0), que é agora incluído como uma biblioteca que você pode adicionar facilmente seus projetos Java, substituindo, portanto, a versão mais antiga.

### <a name="june-29-2016"></a>29 de junho de 2016

O Kit de ferramentas do Azure para IntelliJ - lançamento de junho de 2016 inclui as seguintes melhorias:

* **Requisito de Java 8**. O Kit de ferramentas do Azure para IntelliJ agora requer Java 8, embora esse requisito é somente para o Kit de ferramentas - seus aplicativos podem continuar a usar todas as versões do Java que são suportadas pelo Azure.
* **Suporte para o mais recente JDKs Java**. As versões mais recentes do JDKs Java agora são suportadas pelo Kit de ferramentas do Azure para IntelliJ.
* **Suporte para o SDK Azure v2.9.1**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de ferramentas do Azure para IntelliJ.
* **Exemplos de integrados**. O Kit de ferramentas do Azure para IntelliJ agora apresenta vários aplicativos de exemplo para ajudar os desenvolvedores a começar.
* **Integração de ferramenta de HDInsight**. Ferramentas de HDInsight do Azure agora estão incluídas com o Kit de ferramentas do Azure para IntelliJ. Para obter mais informações, consulte [HDInsight ferramentas plug-in para IntelliJ].
* **Depuração de aplicativos da Web de Java remota**. O Kit de ferramentas do Azure para IntelliJ agora dá suporte a depuração de Java web apps em serviço de aplicativo do Azure remoto.

### <a name="april-12-2016"></a>12 de abril de 2016

O Kit de ferramentas do Azure para IntelliJ - lançamento de abril de 2016 inclui as seguintes melhorias:

* **Suporte para o SDK Azure v2.9.0**. A versão mais recente do SDK do Azure agora é o pré-requisito mínimo para o Kit de ferramentas do Azure para IntelliJ.
* **Usabilidade diversas, melhorias de desempenho e capacidade de resposta relacionadas ao suporte do Azure Web App**. Um número de otimização de desempenho em como o Kit de ferramentas se comunica com resultado Azure em uma interface de usuário mais responde.
* **Capacidade de excluir um contêiner de aplicativo da Web existente no Azure de dentro de IntelliJ**. O Kit de ferramentas do Azure para IntelliJ agora permite que você exclua um contêiner existente do Azure Web sem sair do IntelliJ.

## <a name="see-also"></a>Consulte também ##

Para obter mais informações sobre os kits de ferramentas do Azure para IDEs Java, consulte os links a seguir:

- [Kit de ferramentas de Azure para Eclipse]
  - [Instalando o Kit de ferramentas do Azure para Eclipse]
  - [Criar um aplicativo de Web Olá mundo para Azure no Eclipse]
  - [Novidades no Azure Kit de ferramentas para Eclipse]
- [Kit de ferramentas Azure para IntelliJ]
  - [Instalando o Kit de ferramentas do Azure para IntelliJ]
  - [Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]
  - *Novidades no Azure Kit de ferramentas para IntelliJ (neste artigo)*

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Kit de ferramentas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ferramentas Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo de Web Olá mundo para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo de Web Olá mundo para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalando o Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalando o Kit de ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Azure Kit de ferramentas para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in de ferramentas de HDInsight para IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
