<properties 
   pageTitle="Interface de usuário do Azure contrato móvel - campanha de alcance" 
   description="Laern como criar e gerenciar notificação de envio campanhas usando o contrato de celular do Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Como criar e gerenciar campanhas de notificação de envio
Você pode usar a seção alcance da interface do usuário para criar uma nova campanha de envio com uma fórmula complexa, fornecendo todas as informações que necessárias para enviar uma notificação de envio. As opções de uma campanha de envio ligeiramente variam dependendo dos tipos de quatro campanha: anúncios, votações, envia dados e peças (somente para o Windows Phone).

### <a name="option-applies-to"></a>Opção se aplica a:
- Idiomas: Tudo (anúncios, pesquisas, dados coloca, blocos)
- Campanha: Tudo (anúncios, pesquisas, dados coloca, blocos)
- Notificação: Anúncios, votações
- Conteúdo: Exclusivo para cada tipo de campanha
- Público: Tudo (anúncios, pesquisas, dados coloca, ladrilhos)
- Período: blocos de anúncios, pesquisas,
- Teste: Tudo (anúncios, pesquisas, dados coloca, blocos)
 
![Alcance-Campaign1][20]

## <a name="languages"></a>Idiomas
Você pode usar o menu de lista suspensa de idiomas enviar uma versão diferente do seu Push para dispositivos que são definidos como usar idiomas diferentes. Por padrão, todos os dispositivos receberá a mesma Push, independentemente de qual idioma que eles estão definidos para usar. Os usuários com seu dispositivo definido como um idioma diferente receberão a versão de idioma padrão do envio. Muitas das opções de campanha de envio permitem especificar o conteúdo alternativo para cada um dos idiomas adicionais selecionados. 
 
![Alcance-Campaign2][21]

### <a name="language-differences-apply-to"></a>Diferenças de linguagem aplicam:
- Idiomas: Idiomas exclusivos podem ser selecionados além do idioma padrão
- Campanha: Mesmo para todos os idiomas
- Notificação: Exclusivo para cada idioma além do idioma padrão
- Conteúdo: Exclusivo para cada idioma além do idioma padrão
- Público: Podem ser filtradas por um critério de idioma separados
- Período: mesmo para todos os idiomas
- Teste: Podem ser enviadas para cada idioma por vez
 
### <a name="supported-languages"></a>Idiomas com suporte:
- Árabe (ar) 
- Búlgaro (bg) 
- Catalão (ca) 
- Chinês (zh) 
- Croata (h) 
- Czech (cs) 
- Dinamarquês (da) 
- Holandês (nl) 
- Inglês (en) 
- Finlandês (fi) 
- Francês (fr) 
- Alemão (de) 
- Grego (el) 
- Hebraico (he) 
- Híndi (Olá) 
- Húngaro (hu) 
- Indonésio (id) 
- Italiano () 
- Japonês (ja) 
- Coreano (coreano) 
- Letão (lv) 
- Lituano (lt) 
- Malaio (macrolanguage) (ms) 
- Norueguês (Bokmål) (nb) 
- Polonês (LP) 
- Português (Portugal) 
- Romeno (LIN) 
- Russo (ru) 
- Sérvio (sr) 
- Eslovaco (código) 
- Esloveno (sl) 
- Espanhol (es) 
- Sueco (VA) 
- Tagalo (NFA) 
- Tailandês (ésimo) 
- Turco (tr) 
- Ucraniano (Reino Unido) 
- Vietnamita (vi) 
 
## <a name="campaign"></a>Campanha
Você pode usar a seção de campanha para definir o nome e categoria de campanha, bem como se você planeja ignorar seção audiência de uma campanha de envio e enviar esta campanha via API atinja (e alguns elementos com o API do Push de baixo nível) em vez disso. Categorias podem ser usadas com um modelo de notificação personalizada para notificações do aplicativo de controle com base nas configurações predefinidas. Você pode obter uma lista de "Categorias existentes" por meio da API alcançar.

> Aviso: Se você usar a opção "Ignorar público, envio será enviado aos usuários por meio da API" na seção "Campanha" de uma campanha de alcance, campanha não enviará automaticamente, você precisará enviá-la manualmente por meio da API alcançar.
 
![Alcance-Campaign3][22]
 
### <a name="option-applies-to"></a>Opção se aplica a:
- Nome: tudo
- Categoria: Anúncios, votações
- Ignorar público, envio será enviado aos usuários por meio da API: tudo
 
## <a name="notification"></a>Notificação
Você pode usar a seção de notificação para definir configurações básicas para o envio, incluindo: O título da mensagem do envio, uma imagem do aplicativo, ou se for demissíveis. Muitas configurações de notificação são específicas para a plataforma do seu dispositivo. Você pode selecionar se seu envio será enviado "no aplicativo" ou "sair do aplicativo" ou ambos. (Lembre-se de que os usuários podem "opt-in" ou "recusa" de "sair do aplicativo" envia com o sistema operacional nível em seus dispositivos e Azure Mobile contrato não será capaz de substituir essa configuração. Também Lembre-se de que a API atinja manipula "no aplicativo" e "fora do aplicativo" envia. A API de envio pode ser usada para tratar de "ausência aplicativo" coloca muito.) Coloca pode ser personalizada com figuras ou conteúdo HTML, incluindo links profunda para vinculação fora do seu aplicativo ou para outro local em seu aplicativo (SDK Android 2.1.0 ou posteriores categorias intenção obrigatórias). Você pode alterar o ícone ou iOS selo e enviar o conteúdo da web ou de texto (um pop-up com link de URL conteúdo html para outro local dentro ou fora do aplicativo). Você também pode fazer ligar para dispositivos Android ou vibração com o envio. (Lembre-se de que você precisará correta arquivo para tocar ou vibração um dispositivo de manifesto permissões SDK no Android.) Não há atualmente nenhuma setor padrão para tamanhos de Android "Panorama", desde que tamanhos de tela são diferentes em cada dispositivo, mas 400 x 100 imagens trabalhar em quase qualquer tamanho de tela.

### <a name="delivery-types"></a>Tipos de entrega:
-    Sair do aplicativo apenas: notificação serão entregues quando o usuário não usa o aplicativo.
- O limite da notificação somente de aplicativo requer um certificado da Apple ou Google (certificado APNS ou GCM).
- Do aplicativo somente: A notificação aparece apenas quando o aplicativo está sendo executado.
- A notificação usa o sistema de entrega de Capptain alcançar o usuário. Você pode personalizar totalmente a layout/exibição visual de seu envio.
- A qualquer momento: Essa opção garante que você enviar uma notificação de que aplicativo está em execução ou não.

 
![Alcance-Campaign4][23]

### <a name="option-applies-to"></a>Opção se aplica a:
- Notificação: Anúncios, votações
 
## <a name="content"></a>Conteúdo
Você pode usar a seção Conteúda modificar o conteúdo de anúncios, votações, envia dados e peças (somente para o Windows Phone). A configuração de conteúdo de campanhas de envio é específica para o tipo de campanha. 

### <a name="see-also"></a>Consulte também
- [Documentação de interface do usuário - atinja - Push conteúdo][Link 29]
 
![Alcance-Campaign5][24]

## <a name="audience"></a>Público-alvo
Você pode usar a seção de audiência para definir uma lista padrão de itens para limitar sua campanha ou limites sua campanha com base em critérios personalizados. O conjunto padrão de opções para limitar a sua audiência permite push a usuários novos ou antigos ou somente os usuários de envio nativo. Você também pode definir uma cota para limitar o número de usuários que recebem o envio. Você pode editar manualmente a expressão como sua campanha é filtrada para incluir um ou mais critério para usuários de destino. Você pode digitar manualmente uma expressão de audiência. Uma expressão como essa deve definir explicitamente a relação entre critérios. Um critério é descrito por um identificador que deve começar com uma letra maiuscula e não pode conter espaços. A relação entre os critérios pode ser descrita usando 'e', 'ou', 'not' operadores bem como '(',')'. Exemplo: "Criterion1 ou (Criterion1 e não Criterion2)".

> Observação: Com um grande público incluído em campanhas, lado do servidor direcionamento varredura pode ser lento, especialmente se você tentar iniciar várias campanhas ao mesmo tempo.

- Se possível, só inicie uma campanha por vez.
- No máximo, só inicie campanhas de quatro por vez.
- Notificações por push somente aos usuários ativos (caixa de seleção "envolva somente usuários que podem ser encontrados usando Push nativo" e "Envolva apenas usuários ativos") para que somente os usuários que ainda tem o aplicativo instalado e usá-lo precisará ser verificado.
Depois de sua audiência é definida, você pode usar o botão de simular descobrir quantos usuários receberão esse envio. Isso calculará o número de usuários conhecidos potencialmente direcionados desta audiência (essa é uma estimativa com base em uma amostra aleatória de usuários). Lembre-se de que os usuários que desinstalou o aplicativo também fazem parte desta audiência, mas não podem ser acessados.

### <a name="see-also"></a>Consulte também
- [Interface do usuário documentação - alcance - novo critério de envio][Link 28]

![Alcance-Campaign6][25]

### <a name="edit-expression"></a>Editar expressão
![Alcance-Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Limite de: que sua opção de audiência aplica-se:
- Entre em contato com apenas um subconjunto de usuários: tudo (anúncios, votações, envia dados, blocos)
- Envolva somente os usuários antigos: todos (anúncios, votações, envia dados, ladrilhos)
- Envolva somente novos usuários: tudo (anúncios, votações, envia dados, blocos)
- Envolva somente os usuários ociosos: blocos de anúncios, pesquisas,
- Envolva apenas usuários ativos: tudo (anúncios, votações, envia dados, blocos)
- Envolva somente usuários que podem ser encontrados usando Push nativo: anúncios, votações
 
## <a name="time-frame"></a>Período de tempo
Você pode usar a seção de período de tempo para definir quando o envio será enviado ou você pode deixar o período de tempo em branco para iniciar a campanha imediatamente. Lembre-se de que usar o fuso horário os usuários finais pode iniciar campanha mais cedo do que você espera para seus usuários finais na Ásia e enviar pequenos lotes de coloca um momento até que todos os fusos horários do mundo corresponder o período de tempo definido para a sua campanha um dia. Usar o fuso horário dos usuários finais também pode causar atrasos em campanhas pois ele precisa requisitar o horário do telefone antes de iniciar o envio.

> Observação: Campanhas sem uma data de término pode armazenar em cache coloca localmente e ainda exibi-las após campanhas manualmente concluídas. Para evitar esse comportamento, uma hora de término para campanhas específicas.

### <a name="see-also"></a>Consulte também
- [Atingir - como tutoriais – agendamento][Link 3] 
 
![Alcance-Campaign8][27]

### <a name="settings-apply-to"></a>Configurações se aplicam a:
- Período: blocos de anúncios, pesquisas,
 
## <a name="test"></a>Teste
Você pode usar a seção de teste para enviar este push para seu próprio teste do dispositivo antes de salvar a campanha. Se você tiver configurado qualquer linguagens personalizadas para essa campanha, você pode testar o envio em cada idioma. Você pode configurar um dispositivo de teste de "Minha conta".
> Observação: Nenhum servidor dados são registrados quando você usar o botão "testar" envia, somente os dados serão registrados para campanhas de envio real.

### <a name="see-also"></a>Consulte também
- [Documentação de interface do usuário - minha conta][Link 14]
 
![Alcance-Campaign9][28]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
