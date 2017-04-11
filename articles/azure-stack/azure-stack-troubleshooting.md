<properties
    pageTitle="Solução de problemas do Microsoft Azure pilha | Microsoft Azure"
    description="Azure pilha de solução de problemas."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Solução de problemas do Microsoft Azure pilha

Este documento fornece informações de solução de problemas comuns para pilha do Azure.

Para obter a melhor experiência, certifique-se de que seu ambiente de implantação atende a todos os [requisitos](azure-stack-deploy.md) e [preparativos](azure-stack-run-powershell-script.md) antes de instalar. 

As recomendações para solucionar problemas que estão descritos nesta seção são derivadas de várias fontes e podem ou não podem resolver o problema específico. Se você estiver enfrentando um problema não documentado, verifique o [Fórum do Azure pilha MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para suporte adicional e informações adicionais.

Exemplos de código são fornecidos como estão e não não possível garantir resultados esperados. Esta seção está sujeito edições frequentes atualizações conforme melhorias ao produto são implementadas.

  

## <a name="known-issues"></a>Problemas conhecidos

 - Você pode ver os seguintes não encerrando erros durante a implantação, que afetam o êxito de implantação:
     - "O termo 'C:\WinRM\Start-Logging.ps1' não é reconhecido"
     - "EceAction invocar: não é possível indexar um array nulo" 
     - "InvokeEceAction: não é possível vincular argumento ao parâmetro 'Mensagem' porque ele é uma cadeia de caracteres vazia."
 - Talvez você veja implantação Falha na etapa 60.61.93 com um erro "Aplicativo com identificador 'URI' não encontrado". Esse comportamento é devido a maneira como os aplicativos são registrados no Active Directory do Azure.  Se você receber esse erro, continue da etapa 60.61.93 reexecutar [o script de instalação](azure-stack-rerun-deploy.md) até implantação estiver concluída.
 - Você verá que o recurso de **Conjunto de disponibilidade** no mercado aparece sob a categoria de **VirtualMachine BRAÇO** – essa aparência é apenas um problema superficial.
 - Ao criar uma nova máquina virtual no portal do, na etapa **Noções básicas** , a opção de armazenamento padrão SSD.  Essa configuração deve ser alterada para a unidade de disco rígido ou na etapa **tamanho** de implantação de máquina virtual, você não verá tamanhos de máquina virtual disponíveis para selecionar e continuar a implantação. 
 - Você verá AzureRM PowerShell módulos já não são instalados por padrão na VM MAS CON01 (no TP1 foi nomeado ClientVM). Esse comportamento é por design, porque há um método alternativo para [instalar esses módulos e conectar](azure-stack-connect-powershell.md).  
 - Você verá que o provedor de recursos de **Microsoft.Insights** não é registrado automaticamente para assinaturas de locatários. Se você gostaria de ver o monitoramento de dados para uma máquina virtual implantada como um locatário, execute o seguinte comando do PowerShell (após você [instalar e conectar-se](azure-stack-connect-powershell.md) como um locatário): 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Você verá exportar funcionalidade no portal de grupos de recursos, mas sem texto é exibida e disponível para exportar.      
 - Você pode iniciar uma implantação de recursos de armazenamento maiores que cota disponível.  Essa implantação falha e os recursos de conta serão suspenso.  Há duas opções de correção disponíveis:
     - Administrador do serviço pode aumentar a cota, embora as alterações não serão efetivadas imediatamente e comumente levar uma hora para se propagarem.
     - Administrador de serviço pode criar um plano do complemento com cota adicional que o site principal, em seguida, pode adicionar à assinatura.
 - Ao usar o portal para criar VMs em ambientes de pilha Azure com identidade no 'Azure - China', você não verá tamanhos de máquina virtual disponíveis para selecionar na etapa **tamanho** de implantação de máquina virtual e será possível continuar a implantação.
 - Você pode ver uma falha de implantação no portal do, quando a máquina virtual realmente implantou com êxito.
 - Quando você exclui um plano, a oferta ou a assinatura, VMs não podem ser excluídas.
 - Você verá as extensões de máquina virtual no mercado.
 - Você não pode implantar uma máquina virtual de uma imagem de máquina virtual salva.
 - Locatários poderá ver os serviços que não são incluídos em sua assinatura.  Quando locatários tentarem implantar esses recursos, eles recebem um erro.  Exemplo: Assinatura de locatário inclui somente os recursos de armazenamento.  Locatário verá a opção para criar outros recursos como VMs.  Neste cenário, quando um locatário tenta implantar uma máquina virtual, eles recebem uma mensagem indicando que a máquina virtual não pode ser criada. 
 - Ao instalar o TP2, você não deve ativar o host do sistema operacional no VHD fornecido onde você executar o script de instalação do Azure pilha, ou você pode receber um erro de mensagens informando Windows expirará em breve.


## <a name="deployment"></a>Implantação

### <a name="deployment-failure"></a>Falha de implantação
Se você tiver uma falha durante a instalação, o instalador do Azure pilha permite que você continue uma falha na instalação seguindo as [etapas de implantação de reexecutar](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implantação, a sessão do PowerShell ainda está aberta e não mostrará qualquer saída

Esse comportamento é provavelmente apenas o resultado do comportamento padrão de uma janela de comando do PowerShell, quando ela foi selecionada. A implantação de VDC realmente foi bem-sucedida, mas o script foi pausado ao selecionar a janela. Você pode verificar que isso acontece olhando para a palavra "select" na barra de título da janela de comando.  Pressione a tecla ESC para desmarcá-la e a mensagem de conclusão deve ser mostrada depois dela.

## <a name="templates"></a>Modelos

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Modelo do Azure não implantar em pilha do Azure

Verifique se:

- O modelo deve estar usando um serviço do Microsoft Azure que já está disponível ou na visualização na pilha do Azure.
- As APIs usadas para um recurso específico são suportadas pela instância do Azure pilha local, e que você está buscando um local válido ("local" no Azure pilha Technical Preview (PA) 2, vs "Leste EUA" ou "Índia Sul" no Azure).
- Consulte [Este artigo](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sobre os cmdlets AzureRmResourceGroupDeployment de teste, que captura pequenas diferenças na sintaxe do Gerenciador de recursos do Azure.

Você também pode usar os modelos de pilha Azure já fornecidos no [repositório de GitHub](http://aka.ms/AzureStackGitHub/) para ajudá-lo a começar.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Depois de iniciar o host do meu Microsoft Azure pilha VDC, todos os meus locatários VMs são removidos de Gerenciador Hyper-V e voltar automaticamente após esperar um pouco?

Como o sistema de volta o subsistema de armazenamento e a necessidade de RPs para determinar a consistência. O tempo necessário depende do hardware e Specification sendo usada, mas pode ser algum tempo após a reinicialização do host do locatário VMs para voltar e será reconhecido.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Excluiu alguns máquinas virtuais, mas ainda vê os arquivos VHD no disco. Esse comportamento é esperado?

Sim, esse comportamento é esperado. Ele foi projetado dessa maneira porque:

- Quando você exclui uma máquina virtual, VHDs não são excluídos. Disco é recursos separados no grupo de recursos.
- Quando uma conta de armazenamento é excluída, a exclusão fica visível imediatamente por meio do Azure Gerenciador de recursos (portal, PowerShell), mas os discos que ele pode conter ainda são mantidos em armazenamento até que a coleta de lixo é executado.

Se você vir "órfão" VHDs, é importante saber se elas fazem parte da pasta para uma conta de armazenamento que foi excluída. Se a conta de armazenamento não foi excluída, é normal que eles ainda estão lá.

Você pode ler mais sobre como configurar o limite de retenção em [Gerenciar contas de armazenamento](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Etapas de instalação
As seguintes informações sobre etapas de instalação do Azure pilha podem ser úteis para fins de solução de problemas:  

| Índice | Nome | Descrição|
| ----- | ----- | -----|
|0.11 | (DEP) Validar máquinas físicas | Validando a configuração de hardware e sistema operacional em nós físicas. |
| 0.12 | (DEP) Configurar a rede de máquinas físicas para VDC | Configurar opções de rede virtual e interfaces. |
| 0,14 | (DEP) Implantar domínio | Implante o Active Directory na máquina Virtual. |
| 0,15 | (DEP) Configurar o servidor de domínio | Configure o servidor do domínio com grupos de segurança etc. |
| 0,16 | (DEP) Configurar máquina física | Configure rede, ingressar no domínio e administradores do local de instalação. |
| 0,18 | (PARAR) Configurar Cluster de armazenamento | Criar cluster de armazenamento, crie um servidor de arquivo e de pool de armazenamento. |
| 0.19 | (IDC) Infraestrutura de estrutura de configuração | Configure os pré-requisitos para implantação de tecidos. |
| 0.21 | (LÍQUIDO) Configurar o BGP e NAT | Instala BGP e NAT - necessário somente para um nó. |
| 0.22 | (LÍQUIDO) Configurar NAT e servidor de horário | Sincroniza o servidor de horário e configura NAT entradas. |
| 40.41 | (IDC) Criar convidadas | Crie o gerenciamento VMs. |
| 40.42 | (FBI) Configurar o PowerShell JEA | Configure o PowerShell JEA para todas as funções. |
| 40.43 | (FBI) Configurar a autoridade de certificação de pilha do Azure | Instala autoridade de certificação do Azure pilha. |
| 40.44 | (FBI) Configurar pilha Azure autoridade de certificação | Configura autoridade de certificação do Azure pilha. |
| 40.45 | (LÍQUIDO) Configurar o NC em VMs | Instala NC em convidadas |
| 40.46 | (LÍQUIDO) Configurar NC em VMs | Configurar NC em convidadas |
| 40.47 | (LÍQUIDO) Configurar convidadas | Configure o gerenciamento de VMs com ACLs NC. |
| 60.61.81 | (FBI) Implantar serviços de toque do Azure pilha tecidos - FabricRing pré-requisito | Cria VIPs para FabricRing |
| 60.61.82 | (FBI) Implantar os serviços de toque do Azure pilha tecidos - implantar Cluster de estrutura de toque | Instala e configura o Azure pilha tecidos ligar para Cluster. |
| 60.61.83 | (FBI) Implantar extensões de administração para provedores de recursos | Instalando extensões de administração para provedores de recursos |
| 60.61.84 | (ACS) Configure o armazenamento do Azure consistente em nível de nó. | Instala e configura o armazenamento do Azure consistente em nível de nó. |
| 60.61.85 | (ACS) Configure o armazenamento do Azure consistente em nível de cluster. | Instala e configura o armazenamento do Azure consistente em nível de cluster. |
| 60.61.86 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para InfraServiceController |
| 60.61.87 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para IDC |
| 60.61.88 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para ASAppGateway |
| 60.61.89 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos de controlador de armazenamento |
| 60.61.90 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para HealthMonitoring |
| 60.61.91 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para ECE |
| 60.61.92 | (FBI) Implantar serviços de controlador de toque do Azure pilha tecidos - pré-requisito | Pré-requisitos para PMM |
| 60.61.93 | (Katal) Criar objetos de serviço de AzureStack | Crie aplicativos do Azure gráfico e objetos de serviço no AAD. |
| 60.61.94 | (LÍQUIDO) Configuração GW VMs | Instala GW em convidadas. |
| 60.61.95 | (LÍQUIDO) Configurar GW VMs | Configura GW em convidadas. |
| 60.61.96 | (LÍQUIDO) Implantar iDNS em hosts | Implantar iDNS em hosts de infraestrutura |
| 60.61.97 | (LÍQUIDO) Configurar iDNS | Configurar função iDNS |
| 60.61.98 | (FBI) Configuração WSUS VMs | Instala independente em convidadas. |
| 60.61.99 | (FBI) Configurar WSUS VMs | Configura independente de convidadas. |
| 60.61.100 | (FBI) Configuração VMs do SQL Azure | Instala o SQL Azure server no convidadas |
| 60.61.101 | (Katal) Configure os pré-requisitos para VMs foi. | Configura os pré-requisitos para Microsoft Azure pilha em convidadas. |
| 60.61.102 | (Katal) Configuração foi VMs | Instala o Microsoft Azure pilha em convidadas. |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.121 | (FBI) Implantar controladores e provedores de recursos | Instala provedores de recursos e controladores |
| 60.120.122 | (FBI) Configuração do controlador | Configura controladores |
| 60.120.123 | (Katal) Configurar foi VMs | Configura o Microsoft Azure pilha de convidadas. |
| 60.120.124 | (Katal) Configuração de AAD pilha Azure. | Configura pilha Azure com Azure AD. |
| 60.120.125 | (Katal) Instalar o ADFS | Instala serviços de Federação do Active Directory (AD FS) |
| 60.120.126 | (Katal) Instalar o ADFS/Graph | Instala pilha Azure Graph |
| 60.120.127 | (Katal) Configurar ADFS | Configura os serviços de Federação do Active Directory (AD FS) |
| 60.140.141 | (FBI) Configurar SRP | Configura o provedor de recursos de armazenamento |
| 60.140.142 | (ACS) Configure o armazenamento do Azure consistentes. | Configura o armazenamento do Azure consistente. |
| 60.140.143 | (FBI) Criar contas de armazenamento | Crie todas as contas de armazenamento a ser usado por provedores diferentes. |
| 60.140.144 | (FBI) Registrar uso para SRP | Registre o uso do provedor de armazenamento. |
| 60.140.145 | (IDC) Migrar VMs criadas, Hosts e Cluster para IDC | Migra objetos das VMs, Hosts e Cluster criado para IDC |
| 60.140.146 | (FBI) Configurar o Windows Defender | Configura o Windows Defender |
| 60.160.161 | (MÊS) Configurar o agente de monitoramento | Configura o agente de monitoramento |
| 60.160.162 | (FBI) NRP pré-requisito | Pré-requisitos de instalação NRP |
| 60.160.163 | (FBI) Implantação de NRP | Instalações NRP  |
| 60.160.164 | (FBI) Configuração de NRP | Configura NRP |
| 60.160.165 | (FBI) CRP pré-requisito | Pré-requisitos de instalação CRP |
| 60.160.166 | (FBI) Implantação de CRP | Instalações CRP |
| 60.160.167 | (FBI) Configuração de CRP | Configura CRP |
| 60.160.168 | (FBI) FRP pré-requisito | Pré-requisitos de instalação FRP |
| 60.160.169 | (FBI) Implantação de FRP | Instalações FRP  |
| 60.160.170 | (FBI) Configuração de FRP | Configura FRP |
| 60.160.174 | (FBI) URP pré-requisito | Pré-requisitos de instalação URP |
| 60.160.175 | (FBI) Implantação de URP | Instalações URP  |
| 60.160.176 | (FBI) Configuração de URP | Configura URP |
| 60.160.171 | (FBI) HRP pré-requisito | Pré-requisitos de instalação HRP |
| 60.160.172 | (FBI) Implantação de HRP | Instalações HRP  |
| 60.160.173 | (FBI) Configuração de HRP | Configura HRP |
| 60.160.177 | (KV) KeyVault pré-requisito | Pré-requisitos de instalação KeyVault |
| 60.160.178 | (KV) Implantação de KeyVault | Instalações KeyVault  |
| 60.160.179 | (KV) Configuração de KeyVault | Configura KeyVault | 
| 60.190.191 | (FBI) Configurar Galeria | Configurar Galeria |
| 60.190.192 | (FBI) Configurar serviços de toque tecidos | Configurar serviços de toque tecidos |
| 60.221 | (FBI) VMs de Console de configuração | Servidor de Console de instalações em convidadas. |
| 60.222 | (FBI) VMs de Console de configuração | Mova o conteúdo DVM no console máquina virtual. |
| 251 | Preparar para reinicialização futuros | Política de reinicialização do conjunto |


## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes](azure-stack-FAQ.md)
