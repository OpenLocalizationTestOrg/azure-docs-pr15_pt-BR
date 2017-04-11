<properties
    pageTitle="Serviço de aplicativo do Azure e seu impacto sobre os serviços do Azure existentes"
    description="Explica como o novo serviço de aplicativo do Azure e seus recursos afetam serviços existentes no Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Serviço de aplicativo do Azure e os serviços do Azure existentes

Este artigo descreve as alterações nos serviços do Azure existentes como parte da alteração para reunir vários serviços Azure no [Serviço de aplicativo do Azure](https://azure.microsoft.com/services/app-service/), uma nova oferta integrada.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Visão geral

[Serviço de aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um serviço de nuvem novas e exclusivas que permite que os desenvolvedores criem web e aplicativos móveis para qualquer plataforma e qualquer dispositivo. Serviço de aplicativo é uma solução integrada projetada para simplificar funções de codificação repetidas, integração com sistemas de SaaS e enterprise e automatizar processos corporativos atendendo às suas necessidades de segurança, confiabilidade e escalabilidade.

Serviço de aplicativo reúne os seguinte existentes Azure services - [Serviços Biztalk](https://azure.microsoft.com/services/biztalk-services/) , [Os serviços de celular](https://azure.microsoft.com/services/mobile-services/)e [sites](https://azure.microsoft.com/services/websites/)em um único serviço combinado, ao adicionar novos recursos poderosos.  Serviço de aplicativo permite que você hospede os seguintes tipos de aplicativo:

-   Aplicativos Web
-   Aplicativos móveis
-   Aplicativos de API
-   Aplicativos de lógica

A tabela a seguir explica como existente mapa de serviços Azure ao aplicativo de serviço e os tipos de aplicativo disponíveis dentro dela.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Serviço existente do Azure</th>
<th align="left", style="width:10%">Serviço de aplicativo do Azure</th>
<th align="left", style="width:80%">O que mudou</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites Azure</td>
<td align="left">Aplicativos Web</td>
<td align="left"><li>Sites do Azure, o serviço de aplicativo é terminantemente limitado a alterar o nome de sites para aplicativos Web.
<p><li>Todas as instâncias existentes de sites estão agora Web Apps no aplicativo de serviço.</p>
<p><li>Você pode acessar seus sites existentes por meio do <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portal do Azure</a>, onde você encontrará todos os sites existentes em <em>Aplicativos Web</em>.</p>
<p><li><em>Plano de hospedagem da Web</em> agora é <em>O plano de serviço de aplicativo</em>. Um <em>Plano de serviço de aplicativo</em> pode hospedar qualquer tipo de aplicativo de serviço de aplicativo, como aplicativos Web, móveis, lógica ou API.</p>
<p><li>Azure aplicativo de serviço Web Apps em geral é disponibilidade.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Saiba mais sobre aplicativos Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure serviços móveis</td>
<td align="left">Aplicativos móveis</td>
<td align="left"><p><li>Serviços de celular continue estará disponível como um serviço autônomo e permanecem totalmente suportado.</p>
<p><li>Aplicativos Mobile é um tipo de aplicativo no serviço de aplicativo, que integra todas as funcionalidades de serviços de celular e muito mais.</p>
<p><li>É fácil migrar <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">de serviços de celular para aplicativos Mobile</a>.</p>
<p><li>Como parte do aplicativo de serviço, aplicativos Mobile obter novos recursos além dos serviços de celular, como a integração com locais e sistemas de SaaS, teste slots, WebJobs, opções de escala melhor e mais.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Saiba mais sobre os aplicativos Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Aplicativos de API</td>
<td align="left">
<p><li>Aplicativos de API é um novo tipo de aplicativo no serviço de aplicativo que permite que você facilmente criar e consumir APIs na nuvem.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Saiba mais sobre os aplicativos da API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Aplicativos de lógica</td>
<td align="left">
<p><li>Lógica de aplicativos é um novo tipo de aplicativo no serviço de aplicativo que permite que você facilmente automatizar o processo de negócios.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Saiba mais sobre os aplicativos de lógica</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Serviços BizTalk Azure</td>
<td align="left">Aplicativos de BizTalk API</td>
<td align="left">
<li><p>Serviços BizTalk continue estará disponível como um serviço autônomo e permanecem totalmente suportado.</p>
<li><p>Todos os recursos dos serviços do BizTalk são integrados ao serviço de aplicativo como permitindo que os usuários de aplicativos de API para realizar integração de aplicativo empresarial e cenários de integração B2B com qualquer um dos tipos de aplicativo no aplicativo de serviço</p>
<li><p>Com aplicativos de lógica agora é possível automatizar processos de negócios usando uma experiência de design visual para criar fluxos de trabalho</p></td>
</tr>
</tbody>
</table>

Para saber mais, visite [documentação do serviço de aplicativo](https://azure.microsoft.com/documentation/services/app-service/).
