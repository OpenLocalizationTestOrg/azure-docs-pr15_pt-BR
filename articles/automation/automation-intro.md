<properties
    pageTitle="O que é Azure automação | Microsoft Azure"
    description="Saiba qual valor fornece de automação do Azure e obtenha respostas para perguntas comuns para que você possa começar na criação, usando runbooks e DSC de automação do Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="o que é automação, automação azure, exemplos de automação azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Visão geral de automação Azure

Automação do Microsoft Azure fornece uma maneira para os usuários automatizar as tarefas manuais, demorada, propensa e frequentemente repetidas comumente executadas em um ambiente de nuvem e enterprise. Ele economiza tempo e aumenta a confiabilidade das tarefas administrativas regulares e até mesmo agenda-los para ser executado automaticamente em intervalos regulares. Você pode automatizar processos usando runbooks ou automatizar o gerenciamento de configuração usando a configuração de estado desejado. Este artigo fornece uma visão geral de automação do Azure e responde a algumas perguntas comuns. Você pode consultar a outros artigos nesta biblioteca para obter informações detalhadas sobre os tópicos diferentes.


## <a name="automating-processes-with-runbooks"></a>Automação de processos com runbooks

Um runbook é um conjunto de tarefas que executam algum processo automatizado na automação do Azure. Pode ser um processo simples como iniciar uma máquina virtual e criar uma entrada de log, ou você pode ter um runbook complexa que combina outros runbooks menores para realizar um processo complexo em vários recursos ou até mesmo vários nuvens e em ambientes de local.  

Por exemplo, você pode ter um processo manual existente para truncar um banco de dados do SQL se ele está se aproximando tamanho máximo que inclui várias etapas como se conectar ao servidor, conectar-se ao banco de dados, obter o tamanho atual do banco de dados, verificar se limite excedido truncar e notificar usuário. Em vez de executar cada uma dessas etapas manualmente, você pode criar um runbook que deve executar todas essas tarefas como um único processo. Seria iniciar runbook, forneça as informações necessárias, como o nome do servidor SQL, nome do banco de dados e email do destinatário e, em seguida, ficam enquanto o processo for concluído. 


## <a name="what-can-runbooks-automate"></a>O que pode automatizar runbooks?

Runbooks na automação do Azure são baseados no Windows PowerShell ou fluxo de trabalho do Windows PowerShell, para que eles fazem algo que PowerShell pode fazer. Se um aplicativo ou serviço tiver uma API, um runbook pode trabalhar com ele. Se você tiver um módulo do PowerShell para o aplicativo, você pode carregar módulo em automação do Azure e incluir esses cmdlets em seu runbook. Azure runbooks de automação executar na nuvem Azure e pode acessar qualquer recursos de nuvem ou recursos externos que podem ser acessados da nuvem. Usando o [Operador de Runbook híbrido](automation-hybrid-runbook-worker.md), runbooks pode executar no seu centro de dados locais para gerenciar recursos locais. 


## <a name="getting-runbooks-from-the-community"></a>Obtendo runbooks da comunidade

A [Galeria de Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contém runbooks da Microsoft e a comunidade que você pode usar inalteradas no seu ambiente ou personalizá-los para suas própria fins. Eles também são úteis para como referências para aprender a criar seu próprio runbooks. Você pode até mesmo contribuir seu próprio runbooks na Galeria que você acha que outros usuários podem ser úteis. 


## <a name="creating-runbooks-with-azure-automation"></a>Criando Runbooks com a automação Azure 

Você pode [criar seus próprios runbooks](automation-creating-importing-runbook.md) do zero ou modificar runbooks da [Galeria de Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) para seus próprios requisitos. Há três [tipos de runbook](automation-runbook-types.md) que você pode escolher com base em seus requisitos e a experiência do PowerShell. Se você preferir trabalhar diretamente com o código do PowerShell, você pode usar uma [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) ou [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que você editar offline ou com o [editor de textual](http://msdn.microsoft.com/library/azure/dn879137.aspx) no portal do Azure. Se você preferir editar um runbook sem sejam expostos para o código subjacente, você pode criar um [gráfico runbook](automation-runbook-types.md#graphical-runbooks) usando o [editor de gráfico](automation-graphical-authoring-intro.md) no portal do Azure. 

Prefere assistindo a leitura? Examinar o abaixo de vídeo da Microsoft Ignite sessão de maio de 2015. Observação: Enquanto os conceitos e recursos abordados neste vídeo estão corretos, que automação do Azure avançou muito desde que este vídeo foi gravado, ele agora tem uma interface de usuário mais abrangente no portal do Azure e dá suporte a recursos adicionais.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatizar o gerenciamento de configuração com a configuração de estado desejado 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) é uma plataforma de gerenciamento que permite gerenciar, implantar e impor a configuração para hosts físicos e máquinas virtuais usando uma sintaxe declarativa do PowerShell. Você pode definir configurações em um DSC retirar servidor central que máquinas-alvo podem recuperar e aplicar automaticamente. DSC fornece um conjunto de cmdlets do PowerShell que você pode usar para gerenciar configurações e recursos.  

[Azure automação DSC](automation-dsc-overview.md) é uma solução baseado na nuvem para DSC PowerShell que fornece serviços necessários para ambientes corporativos.  Você pode gerenciar seus recursos de DSC na automação do Azure e aplicar configurações a máquinas virtuais ou físicas que recuperá-los de um servidor de retirar DSC na nuvem Azure.  Ele também fornece serviços de relatório informam que você sobre eventos importantes, como quando nós tiveram deviated de sua configuração atribuída e quando uma nova configuração foi aplicada. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Criar suas próprias configurações de DSC com a automação do Azure

[Configurações de DSC](automation-dsc-overview.md#azure-automation-dsc-terms) especificar o estado desejado de um nó.  Vários nós podem aplicar a mesma configuração para garantir que todos eles mantenham um estado idêntico.  Você pode criar uma configuração usando qualquer texto editor em sua máquina local e importe-os para automação do Azure onde você pode compilá-lo e aplicá-lo nós.


## <a name="getting-modules-and-configurations"></a>Obtendo módulos e configurações 

Você pode obter [módulos do PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contendo cmdlets que você pode usar em seus runbooks e configurações de DSC da [Galeria do PowerShell](http://www.powershellgallery.com/). Você pode iniciar esta galeria do portal do Azure e importar módulos diretamente para a automação do Azure, ou você pode baixar e importá-los manualmente. Não é possível instalar os módulos diretamente do portal do Azure, mas você pode baixá-los instalá-los como faria com qualquer outro módulo. 


## <a name="example-practical-applications-of-azure-automation"></a>Exemplo de aplicativos práticos de automação do Azure 

A seguir estão apenas alguns exemplos do que são os tipos de cenários de automação com a automação do Azure. 

* Crie e copie máquinas virtuais em diferentes assinaturas do Azure. 
* Agendar cópias de arquivos de um computador local como um contêiner de armazenamento de Blob do Azure. 
* Automatize funções de segurança como negar solicitações de um cliente quando for detectado um ataque de negação de serviço. 
* Certifique-se de que máquinas continuamente alinhem com política de segurança configurados.
* Gerencie implantação contínua de código do aplicativo em nuvem e na infraestrutura de local. 
* Crie uma floresta do Active Directory do Azure para seu ambiente de laboratório. 
* Truncar uma tabela em um banco de dados do SQL se DB está se aproximando de tamanho máximo. 
* Atualize configurações de ambiente para um site do Azure remotamente. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Como o Azure automação está relacionada a outras ferramentas de automação?

[Automação de gerenciamento de serviço (SMA)](http://technet.microsoft.com/library/dn469260.aspx) destina-se a automatizar tarefas de gerenciamento na nuvem privada. Ele é instalado localmente em seu data center como um componente do [Pacote do Microsoft Azure](https://www.microsoft.com/en-us/server-cloud/). SMA e automação do Azure usam o mesmo formato de runbook baseado no Windows PowerShell e ao fluxo de trabalho do Windows PowerShell, mas SMA não suporta [runbooks gráficas](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) destina-se a automação dos recursos locais. Ele usa um formato diferente runbook de automação do Azure e automação do gerenciamento de serviço e tem uma interface gráfica para criar runbooks sem a necessidade de qualquer script. Seus runbooks são compostos de atividades de pacotes de integração que são escritos especificamente para Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Onde posso obter mais informações? 

Uma variedade de recursos estão disponíveis para saber mais sobre a automação do Azure e criar seus próprios runbooks. 

* **Biblioteca de automação do Azure** é onde você está agora. Os artigos nesta biblioteca forneceram documentação completa sobre a configuração e a administração de automação do Azure e para a criação de seu próprio runbooks. 
* [Cmdlets do PowerShell do Azure](http://msdn.microsoft.com/library/jj156055.aspx) fornece informações para automatizar operações Azure usando o Windows PowerShell. Runbooks usar esses cmdlets para trabalhar com recursos Azure. 
* [Blog de gerenciamento](https://azure.microsoft.com/blog/tag/azure-automation/) fornece as informações mais recentes sobre automação do Azure e outras tecnologias de gerenciamento da Microsoft. Você deve assinar este blog para se manter atualizado com as últimas da equipe de automação do Azure. 
* [Fórum de automação](http://go.microsoft.com/fwlink/p/?LinkId=390561) permite que você postar perguntas sobre automação do Azure inerentes Microsoft e da comunidade de automação. 
* [Cmdlets de automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx) fornece informações para automatizar tarefas de administração. Ele contém cmdlets para gerenciar contas de automação, ativos, runbooks, DSC.


## <a name="can-i-provide-feedback"></a>Posso enviar comentários? 

**Envie-nos comentários!** Se você estiver procurando por uma solução de runbook de automação do Azure ou um módulo de integração, poste uma solicitação de Script no Centro de Script. Se você tiver comentários ou recurso solicitações de automação do Azure, publique-os em [Voz do usuário](http://feedback.windowsazure.com/forums/34192--general-feedback). Obrigado! 


