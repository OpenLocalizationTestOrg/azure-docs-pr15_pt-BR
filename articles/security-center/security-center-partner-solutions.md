<properties
   pageTitle="Gerenciar soluções de parceiros na Central de segurança do Azure | Microsoft Azure"
   description="Este documento apresenta como o Centro de segurança do Azure permite que você monitor rapidamente o status de integridade de suas soluções de parceiros integrado com sua assinatura do Azure."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Monitoramento soluções de parceiros com o Centro de segurança do Azure

Este documento o orientará como monitorar o status de integridade de suas soluções de parceiros na Central de segurança do Azure.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo. Isso não é um guia passo a passo.

## <a name="monitoring-partner-solutions"></a>Monitoramento soluções de parceiros

O bloco de **soluções de parceiros** no **Centro de segurança** blade permite que você monitor rapidamente o status de integridade de suas soluções de parceiros integrado com sua assinatura do Azure.
![Soluções de parceiros de bloco][1]

O bloco de **soluções de parceiros** exibe o número de soluções de parceiros e um status resumido para essas soluções.

O **STATUS** de uma solução de parceiro pode ser:

- Protegido (verde) - nenhum problema de saúde.
- Não íntegra (vermelho) - há um problema de integridade que requer atenção imediata.
- Parado (laranja) de relatório - a solução parou sua integridade de relatório.
- Status de proteção desconhecido (laranja) - a integridade da solução é desconhecida neste momento devido a um processo falha da adição de um novo recurso à solução existente.
- Não relatados (cinza) - a solução não relatado nada ainda, status de uma solução pode ser não relatados se ele apenas foi conectado e ainda está implantando.

Se não houver nenhuma solução integrada com sua assinatura no bloco determinará que não há nenhuma solução. Selecionar o bloco de **soluções de parceiros** permitirá que você abra a lâmina **recomendações** para implantar soluções de segurança de parceiro.
![Não há soluções de parceiros][2]

Para exibir a integridade de suas soluções de parceiros:

1. Selecione o bloco de **soluções de parceiros** . Um blade é aberta exibindo uma lista de suas soluções de parceiros conectada a Central de segurança.
![Soluções de parceiros][3]

2. Selecione uma solução de parceiro. Neste exemplo, permite selecionar a solução **WAF2 F5** .  Um blade abre mostrando que o status da solução parceiro e a solução associados a recursos. Selecione **console de solução** para abrir a experiência de gerenciamento de parceiro para esta solução.
![Detalhes de solução de parceiro][4]

3. Volte para a lâmina **F5 WAF2** e selecione **o aplicativo de Link**. A lâmina de **Aplicativos de Link** é aberta. Aqui você pode conectar recursos para a solução de parceiro.
![Recursos de link para solução de parceiro][5]

## <a name="see-also"></a>Consulte também
Neste documento, você foram introduzidas para o bloco de **Soluções de parceiros** no Centro de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) — Aprenda a configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) — Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
