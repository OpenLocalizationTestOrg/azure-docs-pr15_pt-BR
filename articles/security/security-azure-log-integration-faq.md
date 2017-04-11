<properties
   pageTitle="Integração do Azure log perguntas Frequentes | Microsoft Azure"
   description="Estas perguntas Frequentes respondem dúvidas sobre a integração de log Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Integração do Azure log perguntas frequentes (FAQ)

Estas perguntas Frequentes respondem dúvidas sobre a integração do registro Azure, um serviço que permite a você para integrar brutos logs de seus recursos Azure seus sistemas de gerenciamento de evento (SIEM) e informações de segurança do local. Essa integração oferece um painel unificado para todos os seus ativos, local ou na nuvem, para que você pode agregar, correlação, analisar e de alerta para eventos de segurança associados com seus aplicativos.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Como posso ver as contas de armazenamento do qual integração do Azure log está recebendo logs de máquina virtual do Azure do?

Execute o comando **azlog lista de origem**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Como posso atualizar a configuração de proxy?

Se sua configuração de proxy não permitir acesso de armazenamento do Azure diretamente, abra a **AZLOG. EXE. CONFIG** arquivo em **c:\Program Files\Microsoft integração de Log do Azure**. Atualize o arquivo para incluir a seção **defaultProxy** com o endereço de proxy de sua organização. Após atualizar, parar e iniciar o serviço usando comandos **azlog líquido parar** e **Iniciar líquido azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações de assinatura de eventos do Windows?

Acrescente a **subscriptionid** ao nome amigável ao adicionar a fonte.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

O evento XML tem os metadados, conforme mostrado abaixo, incluindo a id da assinatura.

![Evento de XML][1]

## <a name="error-messages"></a>Mensagens de erro

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Ao executar o comando **azlog createazureid**, por que recebo o seguinte erro?

Erro:

  *Falha ao criar aplicativo AAD - 72f988bf-86f1-41af-91ab-2d7cd011db37-motivo de locatários = 'Proibido' - mensagem = 'Privilégios suficientes para concluir a operação'.*

**Azlog createazureid** tenta criar um principal de serviço em todos os locatários do Azure AD para as assinaturas no qual o logon do Azure tem acesso a. Se seu logon Azure for apenas um usuário convidado no locatário Azure AD, em seguida, o comando falhar com 'Privilégios suficientes para concluir a operação'. Solicite o administrador de locatários para adicionar sua conta como um usuário no locatário.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Ao executar o comando **azlog autorizar**, por que recebo o seguinte erro?

Erro:

  *Aviso de criação de atribuição de função - AuthorizationFailed: O cliente janedo@microsoft.com' com objeto id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre escopo '/ assinaturas/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

Comando **Azlog autorizar** atribui a função de leitor para o serviço do Azure AD principal (criada com **Azlog createazureid**) para as assinaturas fornecidas. Se o logon do Azure não for um administrador de colegas ou um proprietário da assinatura, ele falhar com mensagem de erro 'Falha de autorização'. Controle de acesso baseado em função Azure (RBAC) de colegas administrador ou proprietário é necessário para concluir esta ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Onde posso encontrar a definição das propriedades no log de auditoria?

Consulte:

- [Operações de auditoria com o Gerenciador de recursos](../resource-group-audit.md)
- [Listar os eventos de gerenciamento de uma assinatura de API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde posso encontrar detalhes sobre alertas de Central de segurança do Azure?

Consulte [Gerenciar e responder a alertas de segurança na Central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como modificar o que é coletado com o diagnóstico de máquina virtual?

Consulte [Usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual executando o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) para obter detalhes sobre como Get, modificar e configurar o diagnóstico do Azure no Windows *(WAD)* configuração. A seguir é um exemplo:

### <a name="get-the-wad-config"></a>Obtenha o config WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modificar o Config WAD

O exemplo a seguir é uma configuração onde somente 4624 Iddoevento e Iddoevento 4625 são coletadas do log de eventos de segurança. Eventos de Antimalware da Microsoft são coletados de log de eventos do sistema. Consulte [consumindo eventos] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) para obter detalhes sobre o uso de expressões XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Definir a configuração de WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Após fazer alterações, verifique a conta de armazenamento para garantir que os eventos corretos são coletados.

Se você tiver dúvidas sobre a integração de Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
