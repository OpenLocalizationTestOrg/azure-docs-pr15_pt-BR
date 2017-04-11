<properties
   pageTitle="Remediar vulnerabilidades de sistema operacional na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **SO remediar vulnerabilidades**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Remediar vulnerabilidades de sistema operacional na Central de segurança do Azure

O Centro de segurança do Azure diariamente analisa seu sistema operacional de máquina virtual (VM) (Symbian) para configurações que podem tornar a máquina virtual mais vulnerável ao ataque e recomenda alterações de configuração para tratar essas vulnerabilidades. Para obter mais informações sobre as configurações específicas de sob monitoramento, consulte a [lista de regras de configuração recomendada](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de segurança recomenda resolver vulnerabilidades quando a configuração do sistema operacional do sua máquina virtual não correspondem às regras de configuração recomendada.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **sistema operacional remediar vulnerabilidades**.
![Remediar vulnerabilidades de sistema operacional][1]

    A lâmina **SO remediar vulnerabilidades** abre e lista suas VMs com configurações de sistema operacional que não correspondem às regras de configuração recomendada.  Para cada máquina virtual, a lâmina identifica:

   - **Regras de falha** - o número de regras que falha de configuração do sistema operacional da VM.
   - **Verificação de última hora** --a data e hora que o Centro de segurança verificados última configuração do sistema operacional da VM.
   - **Estado** – o estado atual da vulnerabilidade:

        - Abrir: Vulnerabilidade não tiver sido tratada ainda
        - Em andamento: Vulnerabilidade no momento está sendo aplicada, nenhuma ação é necessária por você
        - Resolvido: Vulnerabilidade já foi tratada (quando o problema for resolvido, a entrada é esmaecida)
  - **GRAVIDADE** - todas as vulnerabilidades são definidas como uma gravidade do baixo, indicando uma vulnerabilidade deve ser abordada, mas não requer atenção imediata.

Selecione uma máquina virtual. Um blade para essa máquina virtual abre e exibe as regras que tenham falhado.
   ![Regras de configuração com falha][2]

Selecione uma regra. Neste exemplo, permite selecionar a **senha deve atender aos requisitos de complexidade**. Um blade abre descrevendo a regra falha e o impacto. Examine os detalhes e considerar como configurações do sistema operacional serão aplicadas.
  ![Descrição para a regra falhou][3]

  Centro de segurança usa enumeração comuns de configuração (CCE) para atribuir identificadores exclusivos para regras de configuração. As informações a seguir são fornecidas neste blade:

  - NOME - Nome da regra
  - GRAVIDADE – Valor CCE gravidade de críticas, importantes ou aviso
  - CCIED – Identificador exclusivo CCE para a regra
  - Descrição - Descrição da regra
  - VULNERABILIDADE - Explicação de vulnerabilidade ou risco, se a regra não é aplicada
  - IMPACTO – Impacto de negócios quando a regra será aplicada
  - VALOR esperado – Valor esperado quando o Centro de segurança analisa sua configuração de sistema operacional de máquina virtual com a regra
  - OPERAÇÃO de regra – Usada pela Central de segurança durante a análise da configuração do sistema operacional de máquina virtual com a regra de operação de regra
  - VALOR real – Valor retornado após a análise da configuração do sistema operacional de máquina virtual com a regra
  - RESULTADO da avaliação –-resultado da análise: passar, falhas


## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação de Central de segurança "Corrigir SO vulnerabilidades." Você pode examinar o conjunto de regras de configuração [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de segurança usa CCE (enumeração de configuração comuns) para atribuir identificadores exclusivos para regras de configuração. Visite o site [CCE](http://cce.mitre.org) para obter mais informações.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) – localizar blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
