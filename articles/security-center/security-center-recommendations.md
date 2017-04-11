<properties
   pageTitle="Gerenciando recomendações de segurança na Central de segurança do Azure | Microsoft Azure"
   description="Este documento orientará como recomendações na Central de segurança do Azure ajudam a proteger seus recursos Azure e permanecer em conformidade com políticas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>Gerenciando recomendações de segurança na Central de segurança do Azure

Este documento o orientará como usar recomendações na Central de segurança do Azure para ajudá-lo a proteger seus recursos Azure.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="what-are-security-recommendations"></a>Quais são as recomendações de segurança?
Centro de segurança periodicamente analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações. As recomendações orientação-lo no processo de configuração controles necessários.

## <a name="implementing-security-recommendations"></a>Implementação recomendações de segurança

### <a name="set-recommendations"></a>Conjunto de recomendações

[Configuração de diretivas de segurança na Central de segurança do Azure](security-center-policies.md), você vai aprender a:

- Configure políticas de segurança.
- Ative a coleta de dados.
- Escolha quais recomendações para ver como parte de sua política de segurança.

Política recomendações centro atual em torno de atualizações do sistema, regras de linha de base, programas antimalware, [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) em sub-redes e interfaces de rede, auditoria de banco de dados do SQL, criptografia de dados transparente de banco de dados do SQL e firewalls de aplicativo web.  [Definir políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### <a name="monitor-recommendations"></a>Recomendações do monitor
Depois de definir uma política de segurança, a Central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco de **recomendações** na lâmina **Central de segurança** informa que o número total de recomendações identificado pela Central de segurança.

![Bloco de recomendações][1]

Para ver os detalhes de cada recomendação:

1. Clique nas **recomendações lado a lado** na lâmina **Central de segurança** . Abre a lâmina **recomendações** .

As recomendações são mostradas em um formato de tabela em que cada linha representa uma recomendação específica. As colunas da tabela são:

- **Descrição**: explica a recomendação e o que precisa ser feito para solucionar o problema.
- **Recurso**: lista os recursos aos quais essa recomendação se aplica.
- **Estado**: descreve o estado atual da recomendação:
    - **Abrir**: A recomendação ainda não tiver sido tratada ainda.
    - **Em andamento**: A recomendação no momento está sendo aplicada aos recursos e nenhuma ação é necessária por você.
    - **Resolvido**: A recomendação já foi concluída (neste caso, a linha estará esmaecida).
- **GRAVIDADE**: descreve a gravidade do que recomendação específica:
    - **Alta**: uma vulnerabilidade existe com um recurso significativo (como um aplicativo, uma máquina virtual ou um grupo de segurança de rede) e requer atenção.
    - **Médio**: uma vulnerabilidade e etapas adicionais ou não críticos são necessárias para eliminá-la ou para concluir o processo.
    - **Baixo**: uma vulnerabilidade que devem ser abordados, mas não requer atenção imediata. (Por padrão, não são apresentadas recomendações baixos, mas você pode filtrar recomendações baixos se desejar vê-los.)

Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações disponíveis e o que cada uma delas fará se aplicá-lo.

> [AZURE.NOTE] Você desejará compreender o [classic e modelos de implantação do Gerenciador de recursos](../azure-classic-rm.md) para recursos Azure.

|Recomendação|Descrição|
|-----|-----|
|[Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md)|Recomenda-se de que você ativa a coleta de dados na diretiva de segurança para cada uma das suas assinaturas e todas as máquinas virtuais (VMs) em suas assinaturas.|
|[Remediar vulnerabilidades de sistema operacional](security-center-remediate-os-vulnerabilities.md)|Recomenda que você alinhar suas configurações de sistema operacional com as regras de configuração recomendada, por exemplo, não permitir senhas sejam salvas.|
|[Aplicar atualizações do sistema](security-center-apply-system-updates.md)|Recomenda-se de que você implanta ausentes segurança de sistema e atualizações críticas VMs.|
|[Reinicializar após atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Recomenda-se de que você reinicializar uma máquina virtual para concluir o processo de aplicação de atualizações de sistema.|
|[Adicione um firewall de aplicativo web](security-center-add-web-application-firewall.md)|Recomenda-se de que você implante um firewall de aplicativo da web (WAF) para pontos de extremidade da web. Você pode proteger vários aplicativos da web no Centro de segurança, adicionando esses aplicativos para as implantações de WAF existentes. Dispositivos de WAF (criados com o modelo de implantação do Gerenciador de recursos) precisam ser implantado em uma rede virtual separada. Dispositivos de WAF (criados usando o modelo de implantação clássico) são restritos a usar um grupo de segurança de rede. Esse suporte será estendido a uma implantação totalmente personalizada de um dispositivo de WAF (clássico) no futuro. Centro de segurança será recomendável que você provisionar um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos da web em VMs e no ambiente de serviço de aplicativo (ASE). Para saber mais sobre ASE, consulte a [Documentação do ambiente de serviço de aplicativo](../app-service/app-service-app-service-environments-readme.md). |
|[Finalizar proteção do aplicativo](security-center-add-web-application-firewall.md#finalize-application-protection)|Para concluir a configuração de um WAF, o tráfego deve ser reencaminhado para o aparelho de WAF. Seguir essa recomendação concluirá as alterações de configuração necessárias.|
|[Adicione um Firewall de geração próximo](security-center-add-next-generation-firewall.md)|Recomenda-se de que você adicione um Firewall de geração próximo (NGFW) de um parceiro da Microsoft para aumentar sua proteção de segurança.|
|[Rotear o tráfego através de NGFW apenas](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recomenda-se de que você configure regras de grupo (NSG) de segurança de rede que forçarem o tráfego de entrada para sua máquina virtual por meio de seu NGFW.|
|[Instalar a proteção de ponto de extremidade](security-center-install-endpoint-protection.md)|Recomenda-se de que você provisionar programas antimalware VMs (somente para o Windows VMs).|
|[Resolver alertas de integridade de proteção de ponto de extremidade](security-center-resolve-endpoint-protection-health-alerts.md)|Recomenda-se de que você resolver falhas de proteção de ponto de extremidade.|
|[Habilitar grupos de segurança de rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md)|Recomenda-se de que você habilite NSGs em sub-redes ou VMs.|
|[Restringir o acesso por meio de ponto de extremidade de com a Internet](security-center-restrict-access-through-internet-facing-endpoints.md)|Recomenda-se de que você configure regras de tráfego de entrada para NSGs.|
|[Habilitar a auditoria do SQL do servidor](security-center-enable-auditing-on-sql-servers.md)|Recomenda-se de que você ative a auditoria para servidores do SQL Azure (serviço SQL Azure; não incluir apenas SQL em execução no suas máquinas virtuais).|
|[Habilitar a auditoria do SQL do banco de dados](security-center-enable-auditing-on-sql-databases.md)|Recomenda-se de que você ative a auditoria para bancos de dados do SQL Azure (serviço SQL Azure; não incluir apenas SQL em execução no suas máquinas virtuais).|
|[Habilitar criptografia transparente de dados em bancos de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda-se de que você habilite a criptografia para bancos de dados do SQL (somente serviço SQL Azure).|
|[Habilitar o agente de máquina virtual](security-center-enable-vm-agent.md)|Permite que você veja qual VMs exigem o agente de máquina virtual. O agente de máquina virtual deve ser instalado em VMs para provisionar patch varredura, varredura de linha de base e programas de antimalware. O agente de máquina virtual é instalado por padrão para VMs que são implantadas do Azure Marketplace. O artigo [agente de máquina virtual e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente de máquina virtual.|
| [Aplicar criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda-se de que você criptografe seus discos de máquina virtual usando criptografia de disco do Azure (Windows e Linux VMs). Criptografia é recomendada para volumes o sistema operacional e os dados em sua máquina virtual.|
|[Forneça detalhes de contato de segurança](security-center-provide-security-contact-details.md) | Recomenda que você fornecer segurança informações de contato para cada uma das suas assinaturas. Informações de contato são um número de telefone e endereço de email. As informações serão usadas para contatá-lo a se nossa equipe de segurança localiza que seus recursos estão comprometidos. |
| [Atualizar a versão do sistema operacional](security-center-update-os-version.md) | Recomenda-se de que você atualize a versão do sistema operacional (OS) para seu serviço de nuvem para a versão mais recente disponível para sua família do sistema operacional.  Para saber mais sobre os serviços de nuvem, consulte [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que você instale uma solução de avaliação de vulnerabilidade em sua máquina virtual. |
| [Remediar vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite que você veja vulnerabilidades de sistema e aplicativo detectadas pela solução de avaliação de vulnerabilidade instalada em sua máquina virtual. |

Você pode filtrar e descartar recomendações.

1. Clique em **filtro** na lâmina **recomendações** . A lâmina de **filtro** é aberto e selecionar os valores de gravidade e estado que você deseja ver.

    ![Recomendações de filtro][2]

2. Se você determinar que uma recomendação não é aplicável, você pode ignorar a recomendação e filtre-sair do modo de exibição. Há duas maneiras de ignorar uma recomendação. Uma maneira é um item clique com botão direito e selecione **Descartar**. A outra é passe o mouse sobre um item, clique nas reticências que aparecem à direita e selecione **Descartar**. Você pode exibir recomendações Prescindir clicando em **filtro**e, em seguida, selecionando **Dismissed**.

    ![Descartar recomendação][3]

### <a name="apply-recommendations"></a>Aplicar recomendações
Após examinar todas as recomendações, decida quais você deverá se aplicar primeiro. Recomendamos que você use a classificação de gravidade conforme o parâmetro principal para avaliar quais recomendações deve ser aplicado primeiro.

Na tabela de recomendações acima, selecione uma recomendação e examiná-lo como um exemplo de como aplicar uma recomendação.

## <a name="see-also"></a>Consulte também
Neste documento, você introduzidas recomendações de segurança na Central de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) — Aprenda a configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança.
- [Monitoramento soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar postagens no blog sobre segurança do Azure e conformidade.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
