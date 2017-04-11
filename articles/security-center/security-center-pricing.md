<properties
   pageTitle="Centro de segurança preços | Microsoft Azure"
   description="Este artigo fornece informações sobre preços para o Centro de segurança do Azure."
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
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Preços do Centro de segurança do Azure

O Centro de segurança do Azure ajuda você a evitar, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

## <a name="pricing-tiers"></a>Níveis de preços

Central de segurança é oferecida em dois níveis:

- A **camada gratuita** é ativado automaticamente em todas as assinaturas do Azure. A camada gratuita fornece visibilidade o estado de segurança de seus recursos Azure, política de segurança básica, recomendações de segurança e integração com produtos de segurança e serviços de parceiros.
- A **camada padrão** adiciona recursos de detecção de ameaças avançadas, incluindo inteligência de ameaças, análise de comportamento, detecção de anomalias, ocorrências de segurança e relatórios de avaliação de ameaças. Uma **avaliação gratuita de 90 dias** está disponível para a camada padrão.

Para obter mais informações, consulte a Central de segurança [preços página](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Centro de segurança usa armazenamento do Azure para salvar dados de segurança gerados a partir de seu nós protegida. Os custos associados a esse armazenamento não estão incluídos no preço do serviço e cobrados separadamente com regulares [taxas de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Serão cobrados encargos de armazenamento mesmo durante o período de avaliação.

## <a name="try-standard-free-for-90-days"></a>Tente Standard gratuitamente por 90 dias

Uma avaliação gratuita de 90 dias está disponível para a camada padrão. Para obter a versão de avaliação gratuita da camada padrão, selecione o bloco de **política** na lâmina **Central de segurança** . Selecione a assinatura que você deseja atualizar para o padrão. Na lâmina **política de segurança** , selecione **o nível de preços**. Na lâmina **Escolher sua camada de preços** , selecione **padrão – avaliação gratuita**.

![Avaliação gratuita][1]

No final de 90 dias, você deve escolher continuar a usar o serviço, podemos iniciará automaticamente charging para uso.

## <a name="upgrade-to-standard"></a>Atualizar para o padrão

Atualizar para o nível padrão para adicionar a detecção de ameaças avançadas. Para obter a camada padrão, selecione o bloco de **política** na lâmina **Central de segurança** . Selecione a assinatura que você deseja atualizar para o padrão. Na lâmina **política de segurança** , selecione **o nível de preços**. Na lâmina **Escolher sua camada de preços** , selecione **padrão**.

![Nível padrão][2]

## <a name="why-upgrade-to-standard"></a>Por que atualizar para o padrão?

A camada padrão da Central de segurança fornece todos os recursos da camada livre além detecção de ameaças avançadas. Detecção de ameaças avançadas ajuda a identificar ameaças active direcionamento seus recursos Azure e fornece a obtenção de informações necessárias para responder rapidamente.

Centro de segurança utiliza a análise de segurança avançada, que vai além de abordagens baseadas em assinatura. Inovações em grande volume e tecnologias de aprendizado de máquina são aproveitadas para avaliar eventos pela estrutura do inteiro nuvem – detectar ameaças que seriam impossíveis identificar usando abordagens manuais e prever a evolução dos ataques.

Análise de segurança que vêm com o nível padrão é:

- **Inteligência de ameaças** - procura conhecidos atores más usando inteligência de ameaças globais de produtos da Microsoft e serviços, a Microsoft Digital Crimes unidade, o Microsoft Security Response Center e feeds externos
- **Análise de comportamento** - aplica padrões conhecidos para descobrir comportamentos mal-intencionados
- **Detecção de anomalias** - usa o perfil estatísticas para criar uma linha de base histórica. Ele alertas sobre desvios das linhas de base estabelecidas que atendem a um vetor de ataque potencial

Na lâmina **alertas de segurança** abaixo, a Central de segurança detectou um **incidente**de segurança. Um incidente de segurança é uma agregação de todos os alertas para um recurso que alinhar com padrões de corrente kill. Selecionar o incidente de segurança revela mais detalhes sobre o incidente listas e os alertas relacionados. Selecionar um alerta fornece mais informações sobre essa ocorrência.

![Incidente de segurança][3]

O alerta de **comunicação de rede** abaixo fornece detalhes sobre o alerta. Detalhes incluem sua descrição completa, sua gravidade, seu estado atual (que nesse caso é descartado, significando que o usuário levou ação para descartá-la), o recurso atacado e etapas de solução. Também há uma lista de links para os relatórios de inteligência de dados do Microsoft ameaças. Esses relatórios podem ser usados para fins de defesa e correção de segurança.

![Detalhes de alerta de segurança][4]

## <a name="enable-data-collection"></a>Habilitar coleta de dados

Para habilitar a análise de comportamento de máquina virtual, coleta de dados deve ser ativada. Você pode precisar habilitar a coleta de dados quando você acessar o Centro de segurança pela primeira vez ou quando você estiver criando uma política de segurança.

Para validar se a coleta de dados está habilitada, selecione o bloco de **política** . A lâmina de **política de segurança** será aberta listando suas assinaturas Azure. Selecione uma assinatura. Se **o conjunto de dados** estiver desativada, alterá-lo para ativado e salve a alteração. Consulte [Habilitar coleta de dados no Centro de segurança do Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Próximas etapas

- Neste documento, você introduzidas preços para o Centro de segurança. Para obter informações sobre preços adicionais, consulte o Centro de segurança [preços página](https://azure.microsoft.com/pricing/details/security-center/).
- Para saber mais sobre os recursos de detecção avançada do Centro de segurança, consulte [recursos de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md).
- Se você tiver dúvidas sobre como usar o Centro de segurança, consulte as [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md).
- Se você ainda tiver dúvidas sobre como usar o Centro de segurança ou Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png
