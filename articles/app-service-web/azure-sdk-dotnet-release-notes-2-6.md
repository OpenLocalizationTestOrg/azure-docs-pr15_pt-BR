<properties 
   pageTitle="Notas de versão do SDK do Azure para .NET 2.6" 
   description="Notas de versão do SDK do Azure para .NET 2.6" 
   services="app-service/web" 
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

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.6

Este documento contém as notas de versão para o SDK do Azure para .NET 2.6 lançamento. 

Com o Azure SDK 2.6, você pode desenvolver aplicativos de serviço de nuvem (PaaS) direcionamento .NET 4.5.2 ou 4.6 .NET desde que você instalar manualmente o destino do .NET Framework na função de serviço de nuvem. Consulte [instalar .NET em uma função de serviço de nuvem](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Atualizações de barramento de serviço

- Hubs de evento: 

    - Agora permite que o controle de acesso direcionado ao enviar eventos por expor o ponto de extremidade do publisher adicionais para Hubs de evento.
    - Adicionais de estabilidade e aperfeiçoamento adicionado ao recurso de Hubs de evento.
    - Adicionando o suporte do protocolo Amqp sobre WebSocket para mensagens e Hubs de evento.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Ferramentas de HDInsight atualizações do Visual Studio

- **Aprimoramento do IntelliSense**: sugestão de metadados remoto

    Agora HDInsight ferramentas para Visual Studio oferece suporte ao obter metadados remoto quando você estiver editando seu script de seção. Por exemplo, você pode digitar * *Selecione* FROM** e todos os nomes de tabela serão mostrados. Além disso, os nomes de coluna serão mostrados após especificando uma tabela.

- **Suporte a emulador HDInsight**

    Agora HDInsight ferramentas para Visual Studio dá suporte à conexão ao emulador de HDInsight, para que você poderia desenvolver seus scripts de seção localmente sem introduzir qualquer custo, então executar os scripts em relação a seus clusters HDInsight. 

    Para obter mais informações, consulte [Este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **Ferramentas de HDInsight para Visual Studio oferecem suporte para clusters de Hadoop genéricos** (Visualização)

    Agora HDInsight ferramentas para Visual Studio oferecem suporte genéricos clusters do Hadoop, para que você pode usar ferramentas de HDInsight para Visual Studio para fazer o seguinte:

    - conectar ao seu cluster, 
    - escrever seção consulta com suporte aprimorado do IntelliSense/AutoCompletar, 
    - Exiba todos os trabalhos no seu cluster com uma interface de usuário intuitiva. 

    Para obter mais informações, consulte [Este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Atualizações de Cache na função

- **Cache na função** foi atualizado para usar o **SDK do Microsoft Azure armazenamento** versão 4.3. Até agora, o **Cache na função** estava usando a versão 1.7 do SDK de armazenamento do Azure.

    Clientes usando Azure SDK 2.5 ou abaixo deve atualizar para o Azure SDK 2.6 e mover para a nova versão do SDK de armazenamento do Azure. 

    Neste momento o armazenamento do Azure versão 2011-08-18 está agendado para ser removido 1 de agosto de 2016. Qualquer migrações de Cache na função do Azure SDK 2.5 ou abaixo para 2.6 devem ser concluídas por esse período. Para obter mais informações sobre a desativação do armazenamento do Azure versão 2011-08-18, consulte [serviço de armazenamento do Microsoft Azure versão remoção Update: extensão 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Estamos estiver apresentando o afastamento de 30 de novembro de 2016, para o serviço de Cache do Azure gerenciado e Cache do Azure na função. Recomendamos que você migrar para o Cache do Azure relacionada em preparação para este aposentadoria. Para obter mais informações sobre datas e guia de migração, consulte [qual Cache Azure oferta é ideal para mim?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Ferramentas de serviço de aplicativo Azure

Os itens a seguir foram atualizados na versão Azure SDK 2.6.

- Publicação Azure aprimorada para incluir os aplicativos de API do Azure como destino de implantação.
- API aplicativos funcionalidade de provisionamento para permitir que usuários com a funcionalidade de criação e provisionamento de API App.
- Server Explorer é alterado para refletir o novo nó de serviço de aplicativo, com os aplicativos Web, celular e API agrupados por grupo de recursos.
- Adicione gesto de cliente de aplicativo do Azure API adicionado a maioria dos projetos c# que permitirá geração automática de aplicativos de API habilitado para Swagger executado em uma Azure assinatura de usuário.
- Ferramentas de aplicativos de API e nós do serviço de aplicativo no Server Explorer só estão disponíveis no Visual Studio 2013. 

##<a name="azure-resource-manager-tools-updates"></a>Atualizações de ferramentas do Gerenciador de recursos do Azure

As ferramentas do Gerenciador de recurso Azure foram atualizadas para incluir modelos para máquinas virtuais, rede e armazenamento. O JSON experiência de edição foi atualizado para incluir um novo modo de exibição de estrutura de tópicos para modelos e a capacidade de editar os modelos usando trechos JSON. Modelos implantados a partir do Visual Studio usam um script PowerShell fornecido com o projeto, para que as alterações feitas no script serão usadas pelo Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Melhorias de diagnóstico para serviços de nuvem

Azure SDK 2.6 traz de volta suporte para coletar logs de diagnóstico no emulador computação Azure e transferi-las para armazenamento de desenvolvimento. Logs de qualquer diagnóstico (incluindo o rastreamento de aplicativo Logs de rastreamento de eventos do logs do Windows (ETW), contadores de desempenho, infraestrutura logs de eventos de logs e windows) gerado quando o aplicativo está sendo executado no emulador pode ser transferido para armazenamento de desenvolvimento para verificar se o log de diagnóstico está funcionando no seu computador local. 

A conta de armazenamento de diagnóstico agora pode ser especificada no arquivo de configuração (.cscfg) do serviço, tornando mais fácil de usar contas de armazenamento de diagnósticos diferentes para ambientes diferentes. Há algumas diferenças notáveis entre como a cadeia de conexão trabalhou no Azure SDK 2,4 e Azure SDK 2.6. Para obter mais informações sobre como usar a conexão de armazenamento de diagnóstico a cadeia de caracteres e como isso afeta seus projetos consulte [Configurando diagnóstico para serviços de nuvem do Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Alterações significativas

###<a name="azure-resource-manager-tools"></a>Ferramentas do Gerenciador de recurso Azure 

- O tipo de projeto de **Projetos de implantação de nuvem** disponível no Azure SDK 2.5 foi renomeado ao **Grupo de recursos do Azure**.
- Tipo de **Projetos de implantação de nuvem** de projetos criados no Azure SDK 2.5 pode ser usado em 2.6 mas implantar o modelo do Visual Studio falhará. No entanto, a implantação com o script do PowerShell ainda vai funcionar como anteriormente.  Para obter informações sobre como usar **Os projetos de implantação de nuvem** no 2.6 leia esta [postagem](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problemas conhecidos

- Coletar logs de diagnóstico no emulador requer um sistema operacional de 64 bits. Logs de diagnóstico não serão coletadas durante a execução em um sistema operacional de 32 bits. Isso não afetará qualquer outra funcionalidade de emulador. 

- Azure 2.6 SDK lançada em 29/4/2015 tinha dois problemas: 

    - Aplicativo universal não pôde ser carregado no Visual Studio 2015 quando Azure SDK 2.6 foi instalado na máquina.
    - A depuração de um projeto de serviço de nuvem falhará no Visual Studio 2013 e de 2015 Visual Studio onde o Visual Studio não responde e Falha ao exibir uma caixa de diálogo com a mensagem "Configurando diagnóstico para emulador".
    
    Uma atualização do Azure SDK 2.6 foi lançada em 18/5/2015. A versão atualizada é 2.6.30508.1601; ele contém correções para dois problemas descritos acima. Você pode identificar a compilação do SDK no painel de controle -> Programas e recursos -> Ferramentas do Microsoft Azure do Microsoft Visual Studio 2013 – v 2.6. A coluna versão exibirá o número de compilação.

    Se você ainda está enfrentando problemas acima, instale a versão mais recente do Azure 2.6 SDK do [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Consulte também

[Suporte e informações de aposentadoria para o SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
