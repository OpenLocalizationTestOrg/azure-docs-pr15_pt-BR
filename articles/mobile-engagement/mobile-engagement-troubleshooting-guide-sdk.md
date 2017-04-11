<properties 
   pageTitle="Azure contrato móvel guia - SDK de solução de problemas" 
   description="Solucionar problemas de integração de SDK no contrato de celular do Azure" 
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

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guia de solução de problemas para problemas de integração de SDK

A seguir são possíveis problemas que você pode encontrar com como Azure Mobile contrato se integra ao seu aplicativo.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemas SDK descobertos por uma falha em outra área do aplicativo

### <a name="issue"></a>Problema
- Falha de coleta de dados de interface do usuário (em análise, monitoramento, segmentação ou painéis).
- Notificações por push falhas (coloca não funciona no aplicativo, sair do aplicativo ou ambas).
- Avançado falhas de recurso (acompanhamento, localização geográfica ou plataforma que coloca específico não funciona).
- Falhas de API (APIs falhar com frequência silenciosamente sem mensagens de erro).
- Falhas de serviço (nenhuma das Azure Mobile contrato funciona para seu aplicativo).

### <a name="causes"></a>Causas

- A maioria dos problemas que precisam ser resolvidos com o SDK do Azure Mobile Contrato serão descobertos por uma falha no seu aplicativo (como uma falha de conjunto de dados de interface do usuário, falha de envio, falha de recurso avançado, falha de API, falhas do aplicativo ou interrupção do serviço evidente).  
- Se um determinado recurso do Azure Mobile envolvimento nunca tiver trabalhado em seu aplicativo antes, será necessário concluir a integração. 
- Se um determinado recurso do Azure Mobile envolvimento estava trabalhando e interrompido, você precisará atualizar para a última versão com o SDK do Azure Mobile contrato. Lembre-se de que não há uma versão diferente do Azure Mobile contrato SDK para cada plataforma suportada por Azure Mobile contrato (iOS, Windows, Android e Windows Phone).

#### <a name="sdk-integration"></a>Integração do SDK

- Contrato de celular Azure não corretamente integrado no SDK (análise).
- Atingir integrada não corretamente no SDK (no aplicativo e sair do aplicativo envia).
- Certificado expirado ou incorreto produção versus desenvolvimento (somente no iOS).
- GCM ou ADM não corretamente integrados no SDK (Android somente - serviço específico envia).
- Controle não corretamente integrado no SDK (armazenamento de instalar controle).
- Preguiça local ou GPS não corretamente integrado no SDK (direcionamento por localização geográfica).


**Consulte também:**

- [Documentação do SDK - guias de integração][Link 5] 
- [Guia solução de problemas - Push][Link 23]

#### <a name="sdk-upgrade"></a>Atualização do SDK

- Precisa atualizar o SDK para resolver problemas com versões mais antigas do SDK (geralmente relacionadas às versões mais recentes do dispositivo SO).
- Desinstale todas as versões anteriores do aplicativo do seu dispositivo e reinstalar a versão mais recente do seu aplicativo, registrar novamente sua ID de dispositivo de interface do usuário do Azure Mobile contrato para confirmar que o seu dispositivo está usando a versão mais recente do aplicativo.

**Consulte também:**

- [Documentação do SDK - notas de versão](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentação do SDK - guias de atualização](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK outro

- Erros no manifesto do aplicativo "AndroidManifest.xml" podem causar Azure Mobile contrato não (somente para o Android).
- Um problema comum com integração com o SDK e o uso da API é confundir a chave de SDK e a API.

**Consulte também:**

- [Conceitos - Glossário][Link 6]

## <a name="advanced-coding-issues"></a>Advanced codificação problemas

### <a name="issue"></a>Problema
-  Código específico de plataforma não diretamente relacionado ao Azure Mobile contrato pode causar problemas no iOS, Android e Windows Phone.

### <a name="causes"></a>Causas

- Muitas questões codificação com Azure Mobile contrato avançadas são causadas por código específico de plataforma escritas incorretamente não diretamente relacionado ao Azure Mobile contrato. Você precisará consultar a documentação específica para a plataforma que você está desenvolvendo para além de documentação do Azure Mobile contrato (Android, iOS, da Web, Windows e Windows Phone).
- Configurando não corretamente "categorias", impede a vinculação de uma notificação para outro local dentro ou fora do aplicativo (somente para o Android). 
- Não configuração "UIKit.framework" para "opcional" no seu código do iOS, mostra um "Symbol não encontrado erro" e/ou falha em dispositivos iOS antigos (somente no iOS).
- Certificados expirados ou não corretamente usando a versão de desenvolvimento ou produção do certificado, causas problemas de envio (somente no iOS).
- Existem algumas limitações inerentes para uma plataforma que Azure Mobile contrato não pode controlar (como como system center funciona para sair do aplicativo envia no Android e iOS).
- Contrato de celular Azure publica uma lista completa dos pacotes internos usado pelo Azure Mobile contrato para iOS e Android para referência. Tenha em mente que alguns recursos do Azure Mobile contrato são específicos para a plataforma (Android, iOS, da Web, Windows e Windows Phone).

### <a name="see-also"></a>Consulte também

 - [Guia solução de problemas - Push][Link 23] 
 - [Documentação do SDK - notas de versão][Link 5]
 - [Documentação do SDK - guias de atualização][Link 5]

## <a name="application-crashes"></a>Falhas de aplicativos

### <a name="issue"></a>Problema
- Seu aplicativo falha no dispositivo dos usuários finais.

### <a name="causes"></a>Causas

- Informações de falha podem ser visualizadas na *Análise de interface do usuário* ou a *API de análise*
- Você pode localizar a identificação de dispositivo de seu dispositivo de teste e execute a ação mesma que causou seu aplicativo falhar para um usuário final ajudar a identificar a causa do seu falha.
- Problemas conhecidos com o SDK do Azure Mobile contrato que causar aplicativos falhar às vezes são resolvidos pela atualização para a versão mais recente do SDK. Verifique as notas de versão sobre sua plataforma ao investigar a causa uma falha.

### <a name="see-also"></a>Consulte também

- [Documentação do SDK - notas de versão][Link 5]
- [Documentação do SDK - guias de atualização][Link 5]

## <a name="app-store-upload-failures"></a>Falhas de carregamento do aplicativo store

### <a name="issue"></a>Problema
- Erros relacionados ao carregar a versão mais recente do aplicativo Apple, Google ou loja de aplicativos do Windows.

### <a name="causes"></a>Causas

- Aplicativo às vezes armazena aplicativos de bloco com determinados recursos habilitados (por exemplo, Apple Store impede que o uso da IDFV em aplicativos da loja e o repositório de GooglePlay impede que o compartilhamento de aplicativo informações entre os aplicativos). 
- Certifique-se de que você verifique as notas de versão sobre sua plataforma e a versão atual do SDK se você tiver dificuldades para carregar um aplicativo no repositório.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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
 
