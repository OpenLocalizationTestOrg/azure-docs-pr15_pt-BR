<properties
    pageTitle="Histórico de versão do diagnóstico do Azure"
    description="Explicação das alterações em diferentes versões do diagnóstico do Azure como enviada com diferentes versões do Microsoft Azure SDKs."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Histórico de versões de diagnóstico do Microsoft Azure

Começando a usar o diagnóstico do Azure? Consulte [Visão geral do diagnóstico do Azure](azure-diagnostics.md).

Cada versão do Azure SDK normalmente vem com uma nova versão do diagnóstico do Azure. A tabela abaixo descreve as versões de SDK do Azure e o diagnóstico do Azure associadas à versão SDK.



Versão do Azure SDK | Versão do Azure diagnóstico | Modelo
--- | --- | ---
1. x      | 1.0 | plug-in
2.0 para 2,4| 1.0 | "
2.5      | 1.2 | extensão
2.6      | 1.3 | "
2.7      | 1,4 | "
2,8      | 1,5 | "


A versão mais recente é 1,5, que acompanha o Azure SDK 2,8. A versão da extensão de diagnóstico do Azure que acompanha o SDK é usada somente para cenários de emulador local. Qualquer aplicativo implantado usa automaticamente a versão mais recente quando executado no Azure, independentemente de qual versão do SDK do aplicativo é criado com. No entanto, a menos que você instale o SDK mais recente do Azure, você pode não ter todas as ferramentas que ajudam você utilizam os novos recursos.

Vários recursos e alterações descritas a seguir.

## <a name="azure-sdk-28"></a>Azure SDK 2,8
Azure SDK 2,8 adicionado a capacidade de enviar dados de diagnóstico para [Obtenção de informações de aplicativo](./application-insights/app-insights-cloudservices.md) facilitando a diagnosticar problemas em seu aplicativo bem como o nível de sistema e infraestrutura.

## <a name="azure-26-diagnostics-changes"></a>Alterações de diagnóstico do Azure 2.6

Para projetos de serviço de nuvem do Azure SDK 2.6 no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam às versões posteriores do Azure SDK.)

- O emulador local agora dá suporte a diagnóstico. Isso significa que você pode coletar dados de diagnóstico e certifique-se de que seu aplicativo está criando os rastreamentos direito enquanto você está desenvolvendo e testando no Visual Studio. A cadeia de conexão `UseDevelopmentStorage=true` habilita a coleta de dados de diagnóstico enquanto você estiver executando o seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletadas na conta de armazenamento (armazenamento de desenvolvimento).

- A cadeia de conexão de conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada novamente no arquivo de configuração (.cscfg) do serviço. No Azure SDK 2.5 a conta de armazenamento de diagnóstico foi especificada no arquivo diagnostics.wadcfgx.

Há algumas diferenças notáveis entre como a cadeia de conexão trabalhou no Azure SDK 2,4 e anteriores e como ele funciona no Azure SDK 2.6 e versões posteriores.

- No Azure SDK 2,4 e anteriores, a cadeia de conexão foi usada como um tempo de execução pelo plug-in diagnóstico para obter as informações de conta de armazenamento para transferir logs de diagnóstico.

- No Azure SDK 2.6 e posterior, a cadeia de conexão de diagnóstico é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações da conta de armazenamento apropriado durante a publicação. A cadeia de conexão permite que você defina contas de armazenamento diferentes para as configurações de serviço diferente que Visual Studio usará durante a publicação. No entanto, porque o plug-in diagnóstico não está mais disponível (após Azure SDK 2.5), o arquivo de .cscfg por si só não pode habilitar a extensão de diagnóstico. Você precisa habilitar a extensão separadamente por meio de ferramentas como o Visual Studio ou PowerShell.

- Para simplificar o processo de configuração a extensão de diagnóstico com o PowerShell, a saída de pacote do Visual Studio também contém o XML de configuração público para a extensão de diagnóstico para cada função. Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento presentes na configuração do pública. Os arquivos config público são criados na pasta Extensions e siga o padrão PaaSDiagnostics. <RoleName>. PubConfig.xml. Qualquer implantações do PowerShell com base podem usar esse padrão para mapear cada configuração para uma função.

- A cadeia de conexão no arquivo .cscfg também é usada pelo portal do Azure para acessar os dados de diagnóstico para que ele pode aparecer na guia **Monitoring** . A cadeia de conexão é necessária para configurar o serviço para mostrar dados de monitoramento detalhados no portal.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos ao Azure SDK 2.6 e posterior

Ao migrar do Azure SDK 2,5 para Azure SDK 2.6 ou posterior, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo .wadcfgx, em seguida, ela permanecerá lá. Para tirar proveito da flexibilidade do usando contas de armazenamento diferentes para as configurações de armazenamento diferente, você precisará adicionar manualmente a cadeia de conexão ao seu projeto. Se você estiver migrando um projeto do Azure SDK 2,4 ou anterior para Azure SDK 2.6, as cadeias de caracteres de conexão de diagnóstico são preservadas. No entanto, observe as alterações no como cadeias de caracteres de conexão são tratadas no Azure SDK 2.6 conforme especificado na seção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico

- Se uma cadeia de conexão de diagnóstico for especificada no arquivo .cscfg, o Visual Studio o usa para configurar a extensão de diagnóstico ao publicar e ao gerar os arquivos xml de configuração pública durante a compactação.

- Se nenhuma cadeia de caracteres de conexão de diagnóstico for especificada no arquivo .cscfg, em seguida, Visual Studio volta ao usando a conta de armazenamento especificada no arquivo .wadcfgx para configurar a extensão de diagnóstico quando a publicação e gerar os arquivos xml de configuração pública quando embalagem.

- A cadeia de conexão de diagnóstico no arquivo .cscfg tem precedência sobre a conta de armazenamento no arquivo .wadcfgx. Se uma cadeia de conexão de diagnóstico for especificada no arquivo .cscfg, o Visual Studio usa e ignora a conta de armazenamento em .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que significa as "Atualizar armazenamento conexão cadeias de desenvolvimento..." caixa de seleção fazer?

A caixa de seleção **Atualizar cadeias de conexão de armazenamento de desenvolvimento para diagnóstico e cache com as credenciais de conta do Microsoft Azure armazenamento quando a publicação no Microsoft Azure** fornece uma maneira conveniente de atualizar as cadeias de caracteres de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que você marque esta caixa de seleção e especifica a cadeia de conexão de diagnóstico `UseDevelopmentStorage=true`. Quando você publicar o projeto do Azure, Visual Studio atualizará automaticamente a cadeia de conexão de diagnóstico com a conta de armazenamento que você especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de conexão de diagnóstico, em seguida, essa conta é usada.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de recursos de diagnóstico entre Azure SDK 2,4 e anterior e Azure SDK 2,5 e posterior

Se você estiver atualizando seu projeto do Azure SDK 2,4 Azure SDK 2.5 ou posterior, você deve ter em mente as seguintes diferenças de funcionalidade de diagnóstico.

- **APIs de configuração são preteridos** – configuração programação de diagnósticos está disponível no Azure SDK 2,4 ou versões anteriores, mas está obsoleta no Azure SDK 2.5 e versões posteriores. Se sua configuração de diagnóstico está definida atualmente no código, você precisará reconfigurar essas configurações do zero no projeto migrado em ordem para diagnósticos para continuar trabalhando. O arquivo de configuração de diagnóstico do Azure SDK 2,4 é diagnostics.wadcfg e diagnostics.wadcfgx do Azure SDK 2.5 e versões posteriores.

- **Diagnóstico para aplicativos de serviço de nuvem só pode ser configurado no nível de função, não no nível de instância.**

- **Sempre que você implanta seu aplicativo, a configuração de diagnóstico é atualizada** – isso pode causar problemas de paridade se você alterar a configuração de diagnóstico do Server Explorer e reimplanta o aplicativo.

- **No Azure SDK 2.5 e posteriores, falhar despejos estão configurados no arquivo de configuração de diagnóstico, não no código** – se você tiver despejos configurados no código, você precisará transferir manualmente a configuração de código para o arquivo de configuração, porque os despejos não são transferidos durante a migração para Azure SDK 2.6.
