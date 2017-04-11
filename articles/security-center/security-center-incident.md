<properties
   pageTitle="Manipulando incidente de segurança na Central de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a usar os recursos do Centro de segurança do Azure para lidar com ocorrências de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Manipulando incidente de segurança na Central de segurança do Azure 
Separação e investigar os alertas de segurança podem ser demoradas para até mesmo os mais experientes analistas de segurança e para muitos é difícil saber até mesmo por onde começar. Usando [a análise](security-center-detection-capabilities.md) para se conectar a informação entre distintas [alertas de segurança](security-center-managing-and-responding-alerts.md), o Centro de segurança pode fornecer um único modo de exibição de uma campanha de ataque e todos os alertas relacionados – você pode rapidamente entender quais ações o invasor levou e quais recursos foram afetados.

Este documento discute como usar o recurso de alerta de segurança na Central de segurança para ajudá-lo a manipulação de ocorrências de segurança.


## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de segurança, um incidente de segurança é uma agregação de todos os alertas para um recurso que alinhar com padrões de [eliminar cadeia](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Ocorrências de aparecem no bloco de [Alertas de segurança](security-center-managing-and-responding-alerts.md) e blade. Um incidente irá revelar a lista de alertas relacionadas, que permite que você obtenha mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerenciamento de ocorrências de segurança

Você pode examinar suas ocorrências de segurança atual examinando o bloco de alertas de segurança. Acessar o Portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada incidente de segurança:

1. No painel central de segurança, você verá o bloco de **alertas de segurança** .

    ![Alertas de segurança lado a lado no Centro de segurança](./media/security-center-incident/security-center-incident-fig1.png)

2.  Clique neste bloco para expandi-la e se um incidente de segurança for detectado, ele aparecerá em gráfico de alertas de segurança, conforme mostrado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig2.png)

3.  Observe que a descrição de incidente de segurança tem um ícone diferente em comparação com outros alertas. Clique para exibir mais detalhes sobre este incidente.

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig3.png)

4.  Sobre o **incidente** blade você verá mais detalhes sobre este incidente de segurança, que inclui sua descrição completa, sua gravidade (que nesse caso é alta), seu estado atual (neste caso ainda está *ativa*, que indica que o usuário não foi conduzido a uma ação para *Descartar* -isso pode ser feito clicando direito sobre o incidente na lâmina **alertas de segurança** ) , o recurso atacado (neste caso *VM1*), as etapas da correção para o incidente, e no painel inferior você ter os alertas que foram incluídos este incidente. Se você desejar obter mais informações sobre cada alerta, basta clicar nele e outro blade será aberta, conforme mostrado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig4.png)

As informações sobre esta blade vai variar de acordo com o alerta. Leia [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como gerenciar esses alertas. Algumas considerações importantes sobre esse recurso:

- Um novo filtro permite que você personalize seu modo de exibição somente incidente, somente alertas ou ambos. 
- Alerta do mesmo pode existir como parte de um incidente (se aplicável), bem como fique visível como uma alerta de autônomo. 
- Descartar um incidente não irá descartar os alertas relacionados.

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como usar o recurso de incidente de segurança na Central de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Recursos de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md)
- [O Centro de segurança do Azure guia de planejamento e operações](security-center-planning-and-operations-guide.md)
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md)– localizar perguntas frequentes sobre como usar o serviço.
- Postagens no [blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/)– localizar blog sobre conformidade e segurança do Azure.
