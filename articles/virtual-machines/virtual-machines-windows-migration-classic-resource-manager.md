<properties
    pageTitle="Suporte a plataforma migração de recursos de IaaS do clássico para o Gerenciador de recursos do Azure | Microsoft Azure"
    description="Este artigo explica a migração com suporte plataforma de recursos do clássico ao Gerenciador de recursos do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Suporte a plataforma migração de recursos de IaaS do clássico para o Gerenciador de recursos do Azure

Neste artigo, vamos descrevem como podemos estiver ativando migração de infraestrutura como um serviço (IaaS) recursos do clássico para modelos de implantação do Gerenciador de recursos. Você pode ler mais sobre o [Gerenciador de recursos do Azure recursos e benefícios](../azure-resource-manager/resource-group-overview.md). Podemos detalhes sobre como conectar-se a recursos a partir dos modelos de dois implantação coexistam em sua assinatura usando gateways de-to-site de rede virtual. 

## <a name="goal-for-migration"></a>Meta de migração

Gerenciador de recursos permite implantar aplicativos complexos através de modelos, configura máquinas virtuais usando as extensões de máquina virtual e incorpora gerenciamento de acesso e marcação. Gerenciador de recursos de Azure inclui scalable paralela implantação para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implantação também oferece gerenciamento de ciclo de vida de computação, rede e armazenamento de maneira independente. Finalmente, há um foco Ativar segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Quase todos os recursos do modelo clássico de implantação são suportados para computação, rede e armazenamento em Gerenciador de recursos do Azure. Para aproveitar os novos recursos no Gerenciador de recursos do Azure, você pode migrar implantações existentes do modelo de implantação do clássico.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações em suas automação e ferramentas após a migração

Como parte de migrar seus recursos do modelo de implantação do clássico para o modelo de implantação do Gerenciador de recursos, você precisa atualizar sua automação existente ou ferramentas para garantir que continua a trabalhar após a migração.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Significado de migração de recursos de IaaS do clássico para o Gerenciador de recursos

Antes que podemos fazer busca detalhada em detalhes, vamos examinar a diferença entre as operações de dados plano e plano de gerenciamento dos recursos de IaaS.

- *Plano de gerenciamento* descreve as chamadas que vêm para o plano de gerenciamento ou a API para modificar recursos. Por exemplo, operações como criando uma máquina virtual, reiniciar uma máquina virtual e atualizar uma rede virtual com uma nova sub-rede gerenciar os recursos em execução. Eles não afetam diretamente a conexão para as instâncias.
- *Plano de dados* (aplicativo) descreve o tempo de execução do próprio aplicativo e envolve a interação com instâncias que não passam por meio da API do Azure. Acessar seu site ou recuperar dados de uma instância do SQL Server ou um servidor de MongoDB será considerado dados interação plano ou do aplicativo. Copiando um blob de uma conta de armazenamento e acessando um endereço IP público RDP ou SSH na máquina virtual também são plano de dados. Essas operações manter o aplicativo em execução em computação, rede e armazenamento.

>[AZURE.NOTE] Em alguns cenários de migração, plataforma Windows Azure paradas, desaloca e reinicia suas máquinas virtuais. Isso gera um tempo de inatividade curto do plano de dados.

## <a name="supported-scopes-of-migration"></a>Com suporte escopos de migração

Há três escopos de migração que têm como alvo principalmente computação, rede e armazenamento. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (não em uma rede virtual)

No modelo de implantação do Gerenciador de recursos, segurança é aplicada a seus aplicativos, por padrão. Todas as VMs precisam estar em uma rede virtual no modelo do Gerenciador de recursos. Reiniciar a plataforma Windows Azure (`Stop`, `Deallocate`, e `Start`) VMs como parte da migração. Você tem duas opções para as redes virtuais:

- Você pode solicitar a plataforma para criar uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
- É possível migrar a máquina virtual para uma rede virtual existente no Gerenciador de recursos.

>[AZURE.NOTE] Neste escopo de migração, as operações de plano de gerenciamento e as operações de dados plano talvez não consigam por um período de tempo durante a migração.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (em uma rede virtual)

Para a maioria das configurações de máquina virtual, apenas os metadados está migrando entre os modelos de implantação clássico e Gerenciador de recursos. As VMs subjacentes estão executando no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações de plano de gerenciamento podem não ser permitidas por um determinado período de tempo durante a migração. No entanto, o plano de dados continua a trabalhar. Isto é, seus aplicativos em execução na parte superior de VMs (clássicas) não aumentam o tempo de inatividade durante a migração.

Atualmente, não há suporte para as seguintes configurações. Se suporte é adicionado no futuro, algumas VMs nessa configuração talvez provoca o tempo de inatividade (Ir por meio de parada, desalocar e reiniciar operações de máquina virtual).

-   Você tem disponibilidade de mais de um conjunto em um serviço de nuvem único.
-   Você tem um ou mais conjuntos de disponibilidade e VMs que não estão em uma conjunto em um serviço de nuvem única de disponibilidade.

>[AZURE.NOTE] Neste escopo de migração, o plano de gerenciamento talvez não consigam por um período de tempo durante a migração. Para certas configurações conforme descrito anteriormente, ocorre de tempo de inatividade de plano de dados.

### <a name="storage-accounts-migration"></a>Migração de contas de armazenamento

Para permitir que a migração perfeita, você pode implantar VMs do Gerenciador de recursos de uma conta de armazenamento clássico. Com esse recurso, recursos de rede e de computação podem e devem ser migrados independentemente dos contas de armazenamento. Após você migra seu máquinas virtuais e a rede Virtual, você precisa migrar sobre suas contas de armazenamento para concluir o processo de migração. 

>[AZURE.NOTE] O modelo de implantação do Gerenciador de recursos não têm o conceito de imagens de clássico e discos. Quando a conta de armazenamento é imagens migradas clássicas e discos não estão visíveis na pilha do Gerenciador de recursos mas o backup VHDs permanecem na conta de armazenamento. 

## <a name="unsupported-features-and-configurations"></a>Configurações e recursos sem suporte

Atualmente, não suportamos alguns recursos e configurações. As seções a seguir descrevem nossas recomendações ao redor delas.

### <a name="unsupported-features"></a>Recursos sem suporte

Os seguintes recursos não são suportados no momento. Você pode, opcionalmente, remover essas configurações, migrar VMs e, em seguida, habilitar novamente as configurações no modelo de implantação do Gerenciador de recursos.

Provedor de recursos | Recurso
---------- | ------------
Calcular | Discos não associados máquina virtual.
Calcular | Imagens de máquina virtual.
Rede | ACLs de ponto de extremidade.
Rede | Gateways de rede virtual (to site, rota expressa do Azure, gateway do aplicativo, aponte para o site).
Rede | Redes virtuais usando VNet correspondência. (Migrar VNet para ARM e ponto) Saiba mais sobre [VNet correspondência] (... /Virtual-Network/virtual-Network-peering-Overview.MD).
Rede | Perfis do Gerenciador de tráfego.

### <a name="unsupported-configurations"></a>Configurações sem suporte

Atualmente, não há suporte para as seguintes configurações.

Serviço | Configuração | Recomendação
---------- | ------------ | ------------
Gerenciador de recursos | Função com base em acesso controle (RBAC) para recursos clássicos | Porque o URI dos recursos está modificado após a migração, é recomendável que você planeja as atualizações de política RBAC que precisam ocorrer após a migração.
Calcular | Várias sub-redes associados a uma máquina virtual | Atualize a configuração de sub-rede para fazer referência somente sub-redes.
Calcular | Máquinas virtuais que pertencem a uma rede virtual, mas não tiver uma sub-rede explícita atribuída | Opcionalmente, você pode excluir a máquina virtual.
Calcular | Máquinas virtuais que têm alertas, políticas de escala automática | A migração percorre e essas configurações são desconectadas. É altamente recomendável avaliar seu ambiente antes de fazer a migração. Como alternativa, você pode reconfigurar as configurações de alerta após a migração é concluída.
Calcular | Extensões de XML VM (BGInfo 1.*, depuração Visual Studio, implantação da Web e depuração remota) | Não há suporte para isso. É recomendável que você remova essas extensões na máquina virtual para continuar a migração ou eles serão ignorados automaticamente durante o processo de migração.
Calcular | Diagnósticos de inicialização com armazenamento Premium | Desabilite o recurso de diagnóstico de inicialização de VMs antes de continuar com a migração. Você pode reativar diagnóstico de inicialização na pilha do Gerenciador de recursos após a migração é concluída. Além disso, bolhas que estão sendo usadas para captura de tela e seriais logs devem ser excluídas para que você já não é cobrados para esses blobs.
Calcular | Serviços de nuvem que contêm funções da web/trabalhador | Isso não é suportado atualmente.
Rede | Redes virtuais que contêm máquinas virtuais e funções da web/trabalhador |  Isso não é suportado atualmente.
Serviço de aplicativo do Azure | Redes virtuais que contêm ambientes de serviço de aplicativo | Isso não é suportado atualmente.
Azure HDInsight | Redes virtuais que contêm os serviços de HDInsight | Isso não é suportado atualmente.
Serviços de ciclo de vida do Microsoft Dynamics | Redes virtuais que contêm máquinas virtuais que são gerenciadas pelos serviços de ciclo de vida do Dynamics | Isso não é suportado atualmente.
Calcular | Extensões de Central de segurança do Azure com um VNET que tem um gateway VPN ou gateway ER com o servidor DNS de local | O Centro de segurança do Azure instala automaticamente extensões nas máquinas virtuais para monitorar sua segurança e gerar alertas. Essas extensões normalmente obtém instaladas automaticamente se a política de Central de segurança do Azure é ativada a assinatura. Como a migração de gateway não é suportada atualmente e o gateway precisa ser excluída antes de prosseguir com confirmando a migração, o acesso à internet para conta de armazenamento de máquina virtual é perdido quando o gateway é excluído. A migração não continuará quando isso acontece conforme o blob de status do agente de convidado não pode ser preenchido. É recomendável para desativar a política de Central de segurança do Azure com assinaturas 3 horas antes de continuar com a migração.

## <a name="the-migration-experience"></a>A experiência de migração

Antes de começar a experiência de migração, é recomendado o seguinte:

- Certifique-se de que os recursos que você deseja migrar não usam as configurações ou recursos sem suporte. Normalmente a plataforma detecta esses problemas e gera um erro.
- Se você tiver VMs que não estão em uma rede virtual, eles serão interrompidos e desalocados como parte da operação de preparação. Se você não quiser perder o endereço IP público, procure em reserva o endereço IP antes de disparar a operação de preparação. No entanto, se as VMs estiverem em uma rede virtual, elas não são interrompidas e desalocadas.
- Planeje sua migração fora do horário comercial para acomodar quaisquer falhas inesperados que podem ocorrer durante a migração.
- Baixe a configuração atual de seu VMs usando o PowerShell, comandos de interface de linha (comando) ou APIs REST para tornar mais fácil de validação após concluir a etapa de preparação.
- Atualize seus scripts de automação/operationalization para lidar com o modelo de implantação do Gerenciador de recursos antes de iniciar a migração. Opcionalmente, você pode fazer operações de GET quando os recursos estão no estado preparado.
- Avalie as políticas RBAC que são configuradas nos recursos do IaaS clássicos e planejar após a migração é concluída.

O fluxo de trabalho de migração é a seguinte

![Captura de tela que mostra o fluxo de trabalho de migração](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Todas as operações descritas nas seções a seguir são idempotente. Se você tiver um problema diferente de um recurso sem suporte ou um erro de configuração, é recomendável que você repita a preparar, anular ou confirmar a operação. Plataforma Windows Azure tenta a ação novamente.

### <a name="validate"></a>Validar

A operação de validar é a primeira etapa no processo de migração. O objetivo desta etapa é analisar dados no plano de fundo para os recursos em migração e retornar sucesso/falha se os recursos são capazes de migração.

Você selecionar a rede virtual ou o serviço hospedado (se ele não é uma rede virtual) que você deseja validar para a migração.

* Se o recurso não é capaz de migração, plataforma Windows Azure listará todos os motivos por que não há suporte para a migração.

### <a name="prepare"></a>Preparar

A operação de preparação é a segunda etapa no processo de migração. O objetivo desta etapa é simular a transformação dos recursos IaaS de clássico a recursos do Gerenciador de recursos e apresentar isso lado a lado para você visualizar.

Você selecionar a rede virtual ou o serviço hospedado (se ele não é uma rede virtual) que você deseja preparar para a migração.

* Se o recurso não é capaz de migração, plataforma Windows Azure interrompe o processo de migração listas e o motivo pelo qual a operação de preparação falhou.
* Se o recurso é capaz de migração, os bloqueios de primeira plataforma Windows Azure para baixo as operações de plano de gerenciamento para os recursos sob a migração. Por exemplo, você não é capazes de adicionar um disco de dados a uma máquina virtual em migração.

Plataforma Windows Azure inicia a migração de metadados do clássico ao Gerenciador de recursos para os recursos de migração.

Após concluir a operação de preparação, você tem a opção de visualizar os recursos em ambos os clássico e Gerenciador de recursos. Para cada serviço de nuvem no modelo clássico de implantação, plataforma Windows Azure cria um nome de grupo de recursos que possui o padrão `cloud-service-name>-migrated`.

>[AZURE.NOTE] Máquinas virtuais que não estão em uma rede Virtual clássico são interrompidas liberada nesta fase de migração.

### <a name="check-manual-or-scripted"></a>Seleção (manual ou script)

Na etapa seleção, opcionalmente, você pode usar a configuração que você baixou anteriormente para validar que a migração parece correta. Como alternativa, você pode entrar no portal e verificar de ponto as propriedades e recursos para validar gostar migração de metadados.

Se você estiver migrando uma rede virtual, a maioria das configuração de máquinas virtuais não é reiniciada. Para aplicativos nessas VMs, você pode validar que o aplicativo está ainda em funcionamento.

Você pode testar o monitoramento/automação e scripts operacionais para ver se as VMs estão funcionando conforme esperado e se seus scripts atualizados funcionam corretamente. Operações de obter apenas têm suporte quando os recursos estão no estado preparado.

Não há nenhuma janela de tempo de conjunto antes da qual você precisa confirmar a migração. Você pode levar quanto tempo como deseja nesse estado. No entanto, o plano de gerenciamento está bloqueado para esses recursos até que você anular ou confirmar.

Se você vir qualquer problema, você pode sempre anular a migração e voltar para o modelo de implantação clássico. Voltar, plataforma Windows Azure abrirá as operações de plano de gerenciamento dos recursos para que você possa retomar operações normais nessas VMs no modelo clássico de implantação.

### <a name="abort"></a>Anular

Anular é uma etapa opcional que você pode usar para reverter as alterações ao modelo clássico de implantação e interromper a migração.

>[AZURE.NOTE] Esta operação não pode ser executada após você ter acionado a operação de confirmação.  

### <a name="commit"></a>Confirmação

Após concluir a validação, você pode confirmar a migração. Recursos não são mais exibidos no clássico e estão disponíveis apenas no modelo de implantação do Gerenciador de recursos. Os recursos migrados podem ser gerenciados apenas no novo portal.

>[AZURE.NOTE] Esta é uma operação idempotente. Se ele falhar, é recomendável que você repetir a operação. Se você continuar a falhar, crie um tíquete ou crie uma postagem no fórum com uma marca de ClassicIaaSMigration em nosso [Fórum de máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Este plano de migração afeta a qualquer um dos meus serviços existentes ou aplicativos executados no Azure máquinas virtuais?**

Não. As VMs (clássicas) são totalmente compatíveis com os serviços no disponibilidade geral. Você pode continuar a usar esses recursos para expandir o seu espaço no Microsoft Azure.

**O que acontece com Meus VMs se eu não planeja migração no futuro próximo?**

Podemos não são substituição a APIs clássico existentes e o modelo de recursos. Queremos facilitar a migração, considerando os recursos avançados que estão disponíveis no modelo de implantação do Gerenciador de recursos. É altamente recomendável que você examine [alguns dos avanços](virtual-machines-windows-compare-deployment-models.md) que fazem parte de IaaS em Gerenciador de recursos.

**O que esse plano de migração significa para minha ferramentas existentes?**

Atualizar seu ferramentas para o modelo de implantação do Gerenciador de recursos é uma das alterações mais importantes que você precisa considerar em seus planos de migração.

**Quanto tempo será o plano de gerenciamento de tempo de inatividade?**

Depende do número de recursos que estão sendo migrados. Para implantações menores (algumas dezenas de VMs), a migração inteira deve levar menos de uma hora. Para implantações em grande escala (centenas de VMs), a migração pode levar algumas horas.

**Para reverter depois meus recursos migrando comprometidos no Gerenciador de recursos?**

Você pode cancelar sua migração desde que os recursos estão no estado preparado. Não há suporte para reversão após os recursos foram migrados com êxito por meio da operação de confirmação.

**Pode para reverter minha migração se a operação de confirmação falhar?**

Você não pode anular migração se a operação de confirmação falhar. Todas as operações de migração, incluindo a operação de confirmação, são idempotente. Portanto, recomendamos que você repetir a operação após alguns instantes. Se você ainda enfrentar um erro, crie um tíquete ou crie uma postagem no fórum com a marca de ClassicIaaSMigration em nosso [Fórum de máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Tenho comprar outro circuito de rota expressa se eu tiver usar IaaS em Gerenciador de recursos?**

Não. Podemos recentemente habilitado [movendo circuitos rota expressa do clássico para o modelo de implantação do Gerenciador de recursos](../expressroute/expressroute-move.md). Você não precisa comprar um novo circuito de rota expressa se você já tiver um.

**O que acontece se eu tivesse configurado políticas de controle de acesso baseado em função para meus recursos de IaaS clássicos?**

Durante a migração, os recursos transformam de clássico ao Gerenciador de recursos. Portanto, recomendamos que você planeja as atualizações de política RBAC que precisam ocorrer após a migração.

**O que acontece se eu estiver usando recuperação de Site do Azure ou Backup do Azure hoje?**

Para migrar sua máquina Virtual habilitados para backup, consulte [eu tiver feito backup meu VMs clássicas em backup cofre. Agora quero migrar meu VMs do modo clássico para o modo do Gerenciador de recursos. Como posso fazer backup-los em Cofre de serviços de recuperação?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Para validar a minha assinatura ou recursos para ver se elas são capazes de migração?**

Sim. Na opção de suporte de plataforma de migração, a primeira etapa na preparação de migração é validar que os recursos são capazes de migração. Caso a operação de validar falhar, você recebe mensagens de todos os motivos que a migração não pode ser concluída.

**O que acontece se eu tiver um erro de cota durante Preparando os recursos de IaaS para a migração?**

Recomendamos que você anular sua migração e efetue uma solicitação de suporte para aumentar as cotas na região onde você está migrando as VMs. Depois que a solicitação de cota for aprovada, você pode começar a executar as etapas de migração novamente.

**Como faço para relatar um problema?**

Poste suas perguntas sobre migração e problemas em nosso [Fórum de máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. Recomendamos a postagem de todas as suas perguntas neste fórum. Se você tiver um contrato de suporte, você está bem-vindo ao fazer logon tíquete também.

**O que acontece se eu não gostar os nomes dos recursos que a plataforma escolheu durante a migração?**

Todos os recursos que você fornece explicitamente nomes para no modelo clássico de implantação são mantidos durante a migração. Em alguns casos, novos recursos são criados. Por exemplo: uma interface de rede é criada para cada máquina virtual. Atualmente, não suportamos a capacidade de controlar os nomes desses recursos novos criada durante a migração. Faça logon seus votos para esse recurso no [Fórum de comentários Azure](http://feedback.azure.com).

* *recebi uma mensagem *"máquina virtual está relatando o status geral do agente como não está pronto. Portanto, a máquina virtual não pode ser migrada. Certifique-se de que o agente de máquina virtual está relatando o status geral do agente como pronto"* ou *"máquina virtual contém extensão cujo Status não está sendo informado de que a máquina virtual. Portanto, não pode ser migrada essa VM."***

Essa mensagem for recebida quando a máquina virtual não tiver conectividade de saída com a internet. O agente de máquina virtual usa conectividade de saída para acessar a conta de armazenamento do Azure para atualizar o status do agente a cada cinco minutos.


## <a name="next-steps"></a>Próximas etapas
Agora que você entende a migração de recursos de IaaS clássicos para o Gerenciador de recursos, você pode iniciar a migração de recursos.

- [Técnico detalhamento da plataforma suportada migração de clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usar o PowerShell para migrar IaaS recursos do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Use CLI para migrar IaaS recursos do clássico ao Gerenciador de recursos do Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica ao Gerenciador de recursos do Azure usando scripts do PowerShell de comunidade](virtual-machines-windows-migration-scripts.md)
