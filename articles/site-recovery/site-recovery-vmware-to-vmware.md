<properties
    pageTitle="Duplicar local máquinas virtuais VMware ou servidores físicos para um site secundário | Microsoft Azure"
    description="Utilize este artigo para replicar VMs VMware ou Windows/Linux servidores físicos para um site secundário com recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Duplicar local máquinas virtuais VMware ou servidores físicos para um site secundário


## <a name="overview"></a>Visão geral

InMage Scout em recuperação de Site do Azure fornece replicação em tempo real entre sites de VMware local. InMage Scout será incluída no assinaturas de serviço de recuperação de Site do Azure.


## <a name="prerequisites"></a>Pré-requisitos

**Conta do Microsoft Azure**: você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação do Site.


## <a name="step-1-create-a-vault"></a>Etapa 1: Criar um cofre
1. Entrar no [portal do Azure](https://portal.azure.com).
2. Clique em Novo > Gerenciamento > Backup e recuperação de Site (OMS). Como alternativa, você pode clicar em Procurar > Cofre de serviços de recuperação > Adicionar.
3. Em **nome** , especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione um deles.
4. No **grupo de recursos** , crie um novo grupo de recursos ou selecione uma existente. Especifique uma região do Azure para concluir campos obrigatórios. 
5.  Em **local**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte [Preços de recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Se você quiser para acessar rapidamente o cofre no painel clique em fixar no painel de controle e clique em criar.
6. O novo cofre aparecerá no painel de controle > todos os recursos e os serviços de recuperação principal compartimentos blade.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Etapa 2: Configurar o cofre e baixar InMage Scout componentes
7. Os serviços de recuperação do blade compartimentos selecione seu cofre e clique em configurações.
8. Nas **configurações** > **Introdução** clique **Recuperação do Site** > Etapa 1: **Preparar infraestrutura** > **meta de proteção**.
9. Em **meta de proteção** selecione site de recuperação e selecione Sim, com VMware vSphere Hypervisor. Clique em Okey.
10. Na **configuração de Scout**, clique em chave de registro e software de download para baixar InMage Scout 8.0.1 GA. Os arquivos de instalação para todos os componentes necessários estão no arquivo. ZIP baixado.


## <a name="step-3-install-component-updates"></a>Etapa 3: Instalar atualizações de componente

Leia sobre as últimas [atualizações](#updates). Você vai instalar os arquivos de atualização em servidores na seguinte ordem:

1. Servidor de RX se houver
2. Servidores de configuração
3. Servidores de processo
3. Servidores de destino mestre
4. servidores de vContinuum
5. Servidor de origem (Windows e servidor Linux)

Instale as atualizações da seguinte maneira:

1. Baixe o arquivo. zip [Atualizar](https://aka.ms/asr-scout-update4) . Este arquivo zip contém os arquivos a seguir:

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bits de update4 UA para RHEL5, OL5, OL6, SUSE 10, 11 SUSE: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Extrai os arquivos. zip.<br>
3. **Servidor para o RX**: cópia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** no servidor de RX e extraia-lo. Na pasta extraída, execute **instalar**.<br>
4. **Para o servidor de processo do servidor de configuração**: cópia **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** para o servidor de configuração e processo. Clique duas vezes para executá-la.<br>
5. **Servidor de destino de mestre para Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** para o servidor de destino mestre. Clique duas vezes para executá-la. Observe que o agente unificado também é aplicável ao servidor de origem. Você deve instalá-lo no servidor de origem, assim, como mencionado posteriormente na lista.<br>
7. **Para o servidor de vContinuum**: copiar **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** para o servidor vContinuum.  Certifique-se de que você fechou o Assistente de vContinuum. Clique duas vezes no arquivo para executá-lo.<br>
8. **Servidor de destino mestre para Linux**: para atualizar o agente unificado, copiar **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** ao servidor de destino mestre e extrai-lo. Na pasta extraída, execute **instalar**.<br>
9. **Servidor de origem para o Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** para o servidor de origem. Clique duas vezes para executá-la.<br>
10. **Servidor de origem para Linux**: para atualizar o agente unificado, copiar versão correspondente do arquivo UA no servidor Linux e extrai-lo. Na pasta extraída, execute **instalar**.  Exemplo: Para RHEL 6,7 64 bits servidor, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia-lo. Na pasta extraída, execute **instalar**.

## <a name="step-4-set-up-replication"></a>Etapa 4: Configurar replicação
1. Configurar a replicação entre a fonte e VMware sites de destino.
2. Para obter orientação, use a documentação de InMage Scout que é baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

    - [Notas de versão](https://aka.ms/asr-scout-release-notes)
    - [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
    - [Guia do usuário](https://aka.ms/asr-scout-user-guide)
    - [Guia de usuário de RX](https://aka.ms/asr-scout-rx-user-guide)
    - [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Atualizações

### <a name="azure-site-recovery-scout-801-update-4"></a>Atualização de Scout 8.0.1 de recuperação de Site Azure 4
Atualização de Scout 4 é uma atualização cumulativa. Ela tem todas as correções de update1 até update3 e estas correções de bugs novos e aprimoramentos.

**Novo suporte de plataforma** 

- Foi adicionado suporte para vCenter/vSphere 6.0, 6.1 e 6.2
- Foi adicionado suporte para os seguintes sistemas operacionais Linux
    - Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2 
    - CentOS 7.0, 7.1 e 7.2
    - Red Hat Enterprise Linux (RHEL) 6,8
    - CentOS 6,8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é fornecido com o pacote de Scout GA base **InMage_Scout_Standard_8.0.1 GA.zip**. Baixe o pacote de Scout GA portal do, conforme mencionado na [etapa 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Aprimoramentos e correções de bugs** 

- Desligamento aprimorado tratamento para seguir Linux OSes e clones evitar problemas de indesejados sincronize novamente.
    - Red Hat Enterprise Linux (RHEL) 6. x
    - Oracle Linux (OL) 6. x
- Para Linux, conclua o acesso à pasta permissões no diretório de instalação do agente unificado agora são restritas somente para o usuário local.
- No Windows tempo limite problema durante a emissão comuns marca de catálogo de consistência distribuídos em muito carregado aplicativos distribuídos, como clusters SQL e Share Point.
- Log adicionado relacionados fix no instalador base CX.
- Link de download do VMware vCLI 6.0 é adicionada ao instalador base de destino do Windows mestre.
- Adicionados mais verificações e logs de alterações de configurações de rede durante failover e exercícios de DR.
- Informações de retenção em algum momento não são relatadas para CX.  
- Para cluster físico, volume redimensionar operação por meio de vContinuum assistente está com defeito quando aconteceu redução do volume de origem.
- Proteção de cluster falhou com o erro "Falha ao encontrar a assinatura do disco" quando cluster disco é PRDM.
- Falha de servidor de transporte de cxps devido a exceção de fora do intervalo. 
- Nome do servidor e colunas IP agora são ajustáveis na página de instalação de envio do Assistente de vContinuum.
- Aprimoramentos da API de RX
    - Fornece cinco mais recentes disponíveis pontos comuns de consistência (marcas única garantia).
    - Fornece capacidade e detalhes de espaço livre para todos os dispositivos protegidos.
    - Fornece o estado de driver de Scout no servidor de origem. 
    
>[AZURE.NOTE] 
>
>- Pacote básico **InMage_Scout_Standard_8.0.1_GA.zip** agora atualizou installer CX installer base **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** e de destino do Windows mestre base **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Para todas as nova instalação use novos bits de GA CX e destino mestre do Windows.
>- Atualização 4 pode ser aplicada diretamente no 8.0.1 GA.
>- O servidor de configuração e atualizações de RX não podem ser acumuladas depois são aplicados no sistema.

### <a name="azure-site-recovery-scout-801-update-3"></a>Site Azure recuperação Scout 8.0.1 Atualização 3
Atualização 3 inclui as seguintes correções de bugs e aprimoramentos:

- O servidor de configuração e RX falharem registrar o Cofre de recuperação do Site quando eles estão usando um proxy.
- O número de horas que o objetivo de ponto de recuperação (RPO) não está coberto não está recebendo atualizado no relatório de integridade.
- O servidor de configuração não está sincronizando com o RX quando os detalhes de hardware do ESX ou detalhes da rede pode conter caracteres UTF-8.
- Controladores de domínio do Windows Server 2008 R2 falharem ao inicializar após a recuperação.
- Sincronização offline não está funcionando conforme esperado.
- Após failover de máquina virtual (VM), exclusão de replicação par fica presa na UI CX por um longo tempo e os usuários não podem concluir o failback ou retomar a operação.
- Geral instantâneo operações que são feitas pelo trabalho de consistência foram otimizadas para ajudar a reduzir o aplicativo desconecta como clientes SQL.
- O desempenho da ferramenta de consistência (VACP.exe) foi aprimorado, reduzindo o uso de memória que é necessário para criar instantâneos no Windows.
- O envio instalar serviço travar quando a senha for maior que 16 caracteres.
- vContinuum não está verificando e solicitar novas credenciais vCenter quando as credenciais são alteradas.
- No Linux, o Gerenciador de cache de destino mestre (cachemgr) não está baixando arquivos do servidor de processo, o que resulta em otimização de par de replicação.
- Quando a ordem de disco de cluster (MSCS) failover físico não é a mesma em todos os nós, replicação não está definida para alguns dos volumes cluster.
<br/>Observe que o cluster precisa ser reprotected para tirar proveito dessa correção.  
- Funcionalidade SMTP não está funcionando como esperado após RX é atualizado do Scout 7.1 para Scout 8.0.1.
- Estatísticas mais foram adicionadas no log para a operação de reversão controlar o tempo gasto para concluí-la.
- Foi adicionado suporte para sistemas operacionais de Linux no servidor de origem:
    - Atualização de Red Hat Enterprise Linux (RHEL) 6 7
    - CentOS 6 atualizar 7
- O CX e RX UI agora podem mostrar a notificação para o par, que avança no modo bitmap.
- As seguintes correções de segurança foram adicionadas no RX:

**Descrição do problema**|**Procedimentos de implementação**
---|---
Ignorar a autorização por meio de violação de parâmetro|Acesso restrito aos usuários não aplicável.
Falsificação de solicitação intersite|Implementado o conceito de token de página, que gera aleatoriamente para cada página. <br/>Com isso, você verá: <li> Há apenas uma única entrada instância para o mesmo usuário.</li><li>Atualização de página não funciona, ele será redirecionado para o painel.</li>
Carregamento de arquivo mal-intencionado|Arquivos restritos a determinadas extensões. Permitido extensões são: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, meio, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e CEP.
Script de intersite persistente | Adicionado validações de entrada.


>[AZURE.NOTE]
>
>-  Todas as atualizações de recuperação de Site são cumulativas. Atualização 3 tem todas as correções de Update 1 e 2 de atualização. Atualização 3 pode ser aplicada diretamente no 8.0.1 GA.
>-  O servidor de configuração e atualizações de RX não podem ser acumuladas depois são aplicados no sistema.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Atualização de Scout 8.0.1 de recuperação do Azure Site 2 (Atualizar 03 15 de dez)

Correções na atualização 2 incluem:

- **Servidor de configuração**: corrigir para um problema que impedido o recurso de medição gratuito 31 dias de trabalho como esperado quando o servidor de configuração foi registrado em recuperação de Site.
- **Agente de unificado**: corrigir para um problema no Update 1 que resultou na atualização não está sendo instalada no servidor de destino mestre quando ele foi atualizado da versão 8.0 para 8.0.1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Atualização de Scout 8.0.1 de recuperação do Azure Site 1

Atualização 1 inclui as seguintes correções de bugs e novos recursos:

- 31 dias gratuitos de proteção por instância do servidor. Isso permite testar a funcionalidade ou configurar um prova de conceito.
    - Todas as operações no servidor, incluindo failover e failback, são gratuitas para os primeiros 31 dias, a partir do momento em que um servidor primeiro estiver protegido com Scout de recuperação do Site.
    - Do 32nd dia em diante, cada servidor protegido será cobrada de acordo com a taxa de instância padrão de proteção de recuperação de Site do Azure para um site de propriedade do cliente.
    - A qualquer momento, o número de servidores protegidos que atualmente estão sendo cobradas está disponível na página painel do Cofre de recuperação de Site do Azure.
- Suporte adicionado para vSphere Interface de linha de comando (vCLI) 5,5 atualização 2.
- Suporte adicionado para sistemas operacionais de Linux no servidor de origem:
    - RHEL 6 atualizar 6
    - RHEL 5 atualizar 11
    - Atualização de centOS 6 6
    - Atualização de centOS 5 11
- Correções de bugs para atender às seguintes questões:
    - Registro de cofre falha para o servidor de configuração ou RX.
    - Volumes de cluster não aparecem como esperado quando máquinas virtuais em cluster são reprotected quando eles retornarem.
    - Failback falha quando o servidor de destino mestre está hospedado em um servidor de ESXi diferente das máquinas virtuais produção local.
    - Permissões de arquivo de configuração são alteradas quando você atualiza para 8.0.1, que afeta a proteção e operações.
    - O limite de nova sincronização não está aplicado conforme esperado, que leva para o comportamento de replicação inconsistente.
    - As configurações de RPO não estão aparecendo corretamente na interface de servidor de configuração. O valor de dados descompactados incorretamente mostra o valor compactado.
    -  A operação de remoção não exclui conforme esperado no Assistente de vContinuum e replicação não é excluída da interface de servidor de configuração.
    -  No Assistente de vContinuum, o disco está desmarcado automaticamente quando você clica em **detalhes** no modo de exibição disco durante a proteção de máquinas virtuais MSCS.
    - Durante o cenário físico-para-virtual (P2V), serviços de HP necessários, como CIMnotify e CqMgHost, não são movidos para manual na máquina virtual recuperação. Isso resulta em tempo de inicialização.
    - Proteção de máquina virtual Linux falhará quando há mais de 26 discos no servidor de destino mestre.

## <a name="next-steps"></a>Próximas etapas

Poste perguntas que você tem no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
