<properties
   pageTitle="Guia de início rápido do Centro de segurança do Azure | Microsoft Azure"
   description="Este artigo ajuda você a começar rapidamente com o Centro de segurança do Azure orientá-lo através os componentes de gerenciamento de monitoramento e a política de segurança e vinculando você a próximas etapas."
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guia de início rápido do Centro de segurança do Azure

Este artigo ajuda você a rapidamente começar a usar o Centro de segurança do Azure, orientando você através os monitoramento e a política de gerenciamento componentes de segurança da Central de segurança.

> [AZURE.NOTE] Este artigo apresenta o serviço usando uma implantação de exemplo. Este artigo não é um guia passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Centro de segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode inscrever para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

A camada livre da Central de segurança é ativada automaticamente com sua assinatura e oferece visibilidade o estado de segurança de seus recursos Azure. Ele fornece gerenciamento de políticas de segurança básica, recomendações de segurança e integração com produtos de segurança e serviços de parceiros Azure.

Você pode acessar o Centro de segurança a partir do [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Para saber mais sobre o portal do Azure, consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Coleta de dados

Centro de segurança coleta dados de suas máquinas virtuais (VMs) para avaliar seu estado de segurança, recomendações de segurança e ameaças o alerta. Quando você primeiro acessar o Centro de segurança, coleta de dados está habilitada em todas as VMs em sua assinatura. Coleta de dados é recomendada, mas você pode optar por sair desativando a coleta de dados na política de segurança central.

As etapas a seguir descrevem como acessar e usar os componentes do Centro de segurança. Nestas etapas, mostraremos como desativar a coleta de dados se você optar por desativar essa opção.

## <a name="access-security-center"></a>Central de segurança do Access

No portal do, siga estas etapas para acessar o Centro de segurança:

1. No menu **Do Microsoft Azure** , selecione o **Centro de segurança**.
![Menu Azure][1]

2. Se você estiver acessando o Centro de segurança pela primeira vez, a lâmina de **boas-vindas** será aberta. Selecione **Sim! Quero Central de segurança do Azure Launch** para abrir o **Centro de segurança** blade e para habilitar coleta de dados.
![Tela de boas-vindas][10]

3. Depois que você iniciar o Centro de segurança da lâmina boas-vindas ou selecione o Centro de segurança no menu do Microsoft Azure, a lâmina do **Centro de segurança** é aberta. Para facilitar o acesso lâmina **Central de segurança** no futuro, selecione a opção de **blade Pin dashboard** (superior direito).
![Blade de PIN para opção de painel][2]

## <a name="use-security-center"></a>Use a Central de segurança

Você pode configurar políticas de segurança para assinaturas do Azure e grupos de recursos. Vamos definir uma política de segurança para sua assinatura:

1. Na lâmina **Central de segurança** , selecione o bloco de **política** .
![Política de segurança][3]

2. Na lâmina **política de segurança - Definir política por assinatura ou grupo de recursos** , selecione uma assinatura.
3. A lâmina de **política de segurança** , **coleta de dados** está habilitado automaticamente colete logs. A extensão de monitoramento está provisionada em todas as VMs novas e atuais na assinatura. (Você pode deixar de coleta de dados, definindo a **coleta de dados** para **Desativar**, mas isso impede que o Centro de segurança dando a você recomendações e alertas de segurança).
4. Na lâmina **política de segurança** , selecione **Escolher uma conta de armazenamento por região**. Para cada região em que você tiver VMs em execução, você deve escolher a conta de armazenamento onde os dados coletados dessas VMs estão armazenados. Se você não escolher uma conta de armazenamento para cada região, ele será criado para você. Os dados coletados são logicamente isolados do dados de outros clientes por razões de segurança.

     > [AZURE.NOTE] Recomendamos que você habilite a coleta de dados e escolha uma conta de armazenamento no nível de assinatura primeiro. Políticas de segurança podem ser definidas no nível de assinatura Azure e nível de grupo de recursos, mas a configuração da conta de armazenamento e coleta de dados ocorre assinatura somente no nível do.

5. Na lâmina **política de segurança** , selecione a **política de prevenção**. Isso abre a lâmina de **política de prevenção** .
![Política de prevenção][4]

6. Na lâmina **política de prevenção** , ative as recomendações que você deseja ver como parte de sua política de segurança. Exemplos:

 - Configurar **atualizações do sistema** **no** verifica todas as máquinas virtuais com suporte para atualizações de sistema operacional ausentes.
 - Definindo **vulnerabilidades do sistema operacional** para **em** verifica todas as máquinas virtuais com suporte para identificar todas as configurações do sistema operacional que podem tornar a máquina virtual mais vulnerável a ataques.

### <a name="view-recommendations"></a>Exibir recomendações

1. Volte para a lâmina do **Centro de segurança** e selecione o bloco de **recomendações** . Centro de segurança periodicamente analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ela mostra recomendações na lâmina **recomendações** .
![Recomendações na Central de segurança do Azure][5]

2.  Selecione uma recomendação no blade **recomendações** para exibir mais informações e/ou agir para resolver o problema.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Exibir o estado de integridade e a segurança dos seus recursos

1.  Retornar para a lâmina do **Centro de segurança** . O bloco de **integridade de segurança de recursos** contém indicadores de estado de segurança para máquinas virtuais, redes, dados e aplicativos.
2.  Selecione **máquinas virtuais** para exibir mais informações. A lâmina **máquinas virtuais** abre e exibe um resumo de status dos programas de antimalware, atualizações do sistema, reinicialização e vulnerabilidades de sistema operacional das suas VMs.
![O bloco de integridade de recursos no Centro de segurança do Azure][6]

3.  Selecione uma recomendação em **Recomendações de máquina VIRTUAL** para exibir mais informações e/ou agir para configurar controles necessários.
4.  Selecione uma máquina virtual em **máquinas virtuais** para exibir detalhes adicionais.

### <a name="view-security-alerts"></a>Alertas de segurança do modo de exibição

1.  Volte para a lâmina do **Centro de segurança** e selecione o bloco de **alertas de segurança** . A lâmina de **alertas de segurança** abre e exibe uma lista dos alertas. A análise de centro de segurança de seus logs de segurança e a atividade de rede gera esses alertas. Alertas de soluções de parceiros integradas estão incluídos.
![Alertas de segurança na Central de segurança do Azure][7]

    > [AZURE.NOTE] Alertas de segurança só estarão disponíveis se a camada padrão da Central de segurança estiver habilitada. Uma avaliação gratuita de 90 dias da camada padrão está disponível. Consulte [próximas etapas](#next-steps) para obter informações sobre como obter o nível padrão.

2.  Selecione um alerta para exibir informações adicionais. Neste exemplo, vamos selecionar **modificado sistema binário descoberto**. Isso abre blades que fornecem detalhes adicionais sobre o alerta.
![Detalhes de alerta de segurança na Central de segurança do Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Exibir a integridade de suas soluções de parceiros

1. Retornar para a lâmina do **Centro de segurança** . O bloco de **soluções de parceiros** permite monitorar, num relance, o status de integridade de suas soluções de parceiros integrada com sua assinatura do Azure.
2. Selecione o bloco de **soluções de parceiros** . Um blade abre e exibe uma lista de suas soluções de parceiros conectada a Central de segurança.
![Soluções de parceiros][9]

3. Selecione uma solução de parceiro. Neste exemplo, vamos selecionar a solução **WAF F5** .  Um blade é aberta e mostra o status da solução parceiro e recursos associados da solução. Selecione **console de solução** para abrir a experiência de gerenciamento de parceiro para esta solução.

## <a name="next-steps"></a>Próximas etapas
Este artigo introduziu a você os monitoramento e a política de gerenciamento componentes de segurança da Central de segurança. Agora que você estiver familiarizado com o Centro de segurança, tente as seguintes etapas:

- Configure uma política de segurança para sua assinatura do Azure. Para saber mais, consulte [configuração de diretivas de segurança na Central de segurança do Azure](security-center-policies.md).
- Use as recomendações no Centro de segurança para ajudar a proteger seus recursos Azure. Para saber mais, consulte [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).
- Revisar e gerenciar seus alertas de segurança atual. Para saber mais, consulte [Gerenciar e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md).
- Saiba mais sobre os [recursos de detecção de ameaças avançados](security-center-detection-capabilities.md) que vêm com a [camada padrão](security-center-pricing.md) da Central de segurança. Uma avaliação gratuita de 90 dias da camada padrão está disponível.
- Se você tiver dúvidas sobre como usar o Centro de segurança, consulte as [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
