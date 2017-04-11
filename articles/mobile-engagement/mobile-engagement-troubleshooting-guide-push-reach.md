<properties 
   pageTitle="Azure contrato móvel guia - Push/alcance de solução de problemas" 
   description="Solucionando problemas de interação e notificação de usuário no contrato de celular do Azure" 
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

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guia de solução de problemas de envio e atinja problemas

A seguir são possíveis problemas que você pode encontrar com como o Azure Mobile contrato envia informações para seus usuários.
 
## <a name="push-failures"></a>Falhas de envio

### <a name="issue"></a>Problema
- Coloca não funciona (no aplicativo, sair do aplicativo ou ambas).

### <a name="causes"></a>Causas
- Uma falha de envio muitas vezes é uma indicação que Azure Mobile contrato, alcance ou outro recurso avançado do Azure Mobile envolvimento não está corretamente integrado ou que uma atualização é necessária no SDK para corrigir um problema conhecido com uma nova plataforma de sistema operacional ou dispositivo.
- Teste apenas um envio no aplicativo e apenas um envio de fora do aplicativo para determinar se algo é um problema no aplicativo ou sair do aplicativo.
- Teste de interface do usuário e a API como uma etapa de solução de problemas para ver quais informações de erro adicionais estão disponíveis dois lugares.
- Sair do aplicativo coloca não funcionarão, a menos que o contrato de celular do Azure e alcance são integrados no SDK.
- Coloca não funcionará se certificados não são válidos, ou estiver usando produção versus desenvolvimento corretamente (somente no iOS). (**Observação:** "sair do aplicativo" notificações por push podem não ser entregue para o iOS, se você tiver os dois o desenvolvimento (DES) e versões de produção (produção) do seu aplicativo instalada no mesmo dispositivo, desde que o token de segurança associado ao seu certificado podem ser invalidadas pela Apple. Para resolver esse problema, desinstale as versões de produção e de desenvolvimento do seu aplicativo e instalar novamente somente a versão em seu dispositivo.)
- Sair do envio de aplicativo contagens são tratadas de maneira diferente em diferentes plataformas (iOS mostra menos informações que Android se coloca nativa está desabilitadas em um dispositivo, a API pode fornecer mais informações do que a interface do usuário em estatísticas de envio).
- Sair do aplicativo coloca pode ser bloqueadas por clientes no nível de sistema operacional (iOS e Android).
- Sair do aplicativo coloca serão mostradas como desativado na interface do usuário do Azure Mobile contrato se eles não são integrados corretamente, mas podem falhar silenciosamente da API.
- No aplicativo coloca não funcionarão, a menos que o contrato de celular do Azure e alcance são integrados no SDK.
- Coloca GCM e ADM não funcionarão, a menos que o contrato de celular do Azure e servidor específico são integrados no SDK (somente para o Android).
- No aplicativo e sair do aplicativo coloca deve ser testada separadamente para determinar se é um problema de envio ou alcance.
- No aplicativo coloca exige que o aplicativo seja aberta a ser recebida.
- No aplicativo coloca costumam ser a configuração para ser filtrados por uma marca de informações de aplicativo opt-in ou opt-out.
- Se você usar uma categoria personalizada no alcance para exibir as notificações do aplicativo, você precisará seguir o ciclo de vida correto da notificação ou então a notificação não pode ser limpos quando o usuário descartá-la.
- Se você iniciar uma campanha sem data final e um dispositivo recebe o aplicativo em notificação mas não não exibi-lo ainda, o usuário ainda serão receber a notificação na próxima vez que fizerem logon no aplicativo, mesmo se você finalizar manualmente a campanha.
- Para problemas com a API do Push, confirme que você realmente quer usar a API do Push em vez da API atinja (desde a API atinja é usada com mais frequência) e que você não é confundir os parâmetros "carga" e "notificação".
- Teste sua campanha de envio com os dois um dispositivo conectado via WIFI e 3G para eliminar a conexão de rede como uma fonte de possível problemas.

## <a name="push-testing"></a>Notificações por push teste

### <a name="issue"></a>Problema
- Coloca pode ser enviadas para um dispositivo específico com base em uma ID de dispositivo.

### <a name="causes"></a>Causas

- Dispositivos de teste são configuração diferente para cada plataforma, mas causando um evento em seu aplicativo em um dispositivo de teste e procurando sua ID de dispositivo no portal devem funcionar para localizar sua ID de dispositivo para todas as plataformas.
- Dispositivos de teste funcionam de maneira diferente com IDFA versus IDFV (somente no iOS).


## <a name="push-customization"></a>Personalização de envio

### <a name="issue"></a>Problema
- Advanced push conteúdo de item não funcionará (crachá, tocam, vibração, imagem, etc.).
- Links de coloca não funcionam (fora do aplicativo, no aplicativo, para um site, em um local no aplicativo).
- Estatísticas de envio mostram que um envio não foi enviado para quantas pessoas conforme esperado (demais ou insuficiente).
- Envio duplicado e recebidos duas vezes.
- Não pode registrar o dispositivo de teste do Azure Mobile contrato envia (com seu próprio aplicativo de produção ou desenvolvimento).

### <a name="causes"></a>Causas

- Para vincular a um local específico no aplicativo requer "categorias" (somente para o Android).
- Esquemas de vinculação profunda para redirecionar usuários para um local alternativo após clicar em uma notificação de envio precisam ser criados em e gerenciadas pelo seu aplicativo e o dispositivo de sistema operacional não pelo contrato Mobile diretamente. (**Observação:** sair do aplicativo notificações não é possível vincular diretamente em locais de aplicativo com iOS como podem Android.)
- Servidores de imagem externa precisam ser capazes de usar HTTP "GET" e "Cabeça" para panorama envia para trabalhar (somente para o Android).
- No seu código, você pode desativar o agente do Azure Mobile contrato quando o teclado é aberto e que o seu código reativar o agente do Azure Mobile contrato quando o teclado é fechado para que o teclado não afeta a aparência da sua notificação (somente no iOS).
- Alguns itens não funcionam no teste simulações, mas somente campanhas reais (crachá, tocam, vibração, imagem, etc.).
- Nenhum dado de lado do servidor é registrado quando você usar o botão "testar" coloca. Somente os dados serão registrados para campanhas de envio real.
- Para ajudar a isolar o problema, solucionar problemas com: testar, simular e uma campanha real desde que cada um deles funcionam de maneira um pouco diferente.
- O período de tempo que seu "no aplicativo" e campanhas de "qualquer hora" estão agendadas para ser executada pode afetar números de entrega, desde que uma campanha só serão entregues para os usuários que são "no aplicativo" durante a execução de campanha (e os usuários que têm suas configurações de dispositivo configuradas para receber notificações "sair do aplicativo").
- As diferenças entre como Android e iOS tratar fora notificações do aplicativo dificulta diretamente comparar estatísticas de push entre a versão Android e iOS do seu aplicativo. Android fornece mais informações de nível de notificação do sistema operacional que faz do iOS. Android relatórios quando uma notificação nativa é recebida, clicada ou excluída no Centro de notificação, mas iOS não relatar essas informações, a menos que a notificação é clicada. 
- O principal motivo "enviados" números são diferentes diferente de "entregue" números para alcançar campanhas é que "no aplicativo" e "sair do aplicativo" notificações são contadas diferente. "No aplicativo" notificações são tratadas contrato do celular, mas "sair do aplicativo" notificações são tratadas pelo Centro de notificação no sistema operacional do seu dispositivo.

## <a name="push-targeting"></a>Notificações por push-alvo

### <a name="issue"></a>Problema
- Integrado direcionamento não funciona como esperado.
- Direcionamento de marca de informações de aplicativo não funciona como esperado.
- Localização geográfica direcionamento não funciona como esperado.
- Opções de idioma não funcionam como esperado.

### <a name="causes"></a>Causas

- Certifique-se de que você carregou marcas de informações de aplicativo por meio do Azure Mobile contrato UI ou API.
- A limitação a velocidade de envio ou cota de envio no nível do aplicativo, ou limitar o público no nível da campanha pode impedir que uma pessoa recebendo um envio específico, mesmo se eles atendam a seus critérios de direcionamento. 
- Definindo um "idioma" é diferente direcionamento com base no país ou localidade, que também é diferente de direcionamento com base em localização geográfica com base em um local de telefone ou GPS.
- A mensagem no "idioma padrão" é enviada para qualquer cliente que não tem seu dispositivo definido como um dos idiomas alternativos que você especifica.


## <a name="push-scheduling"></a>Agendamento de envio

### <a name="issue"></a>Problema
- Agendamento de envio não funciona como esperado (enviada muito antecipado ou atrasadas).

### <a name="causes"></a>Causas

- Fusos horários pode problemas com o agendamento, especialmente quando usando o fuso horário dos usuários finais.
- Recursos avançados de envio podem atrasar coloca.
- Direcionamento com base no telefone configurações (em vez de marcas de informações de aplicativo) podem atrasar envia desde que talvez seja necessário solicitar dados do tempo real de telefone antes de enviar um envio Azure Mobile contrato.
- Campanhas criadas sem uma data de término armazenam o envio localmente no dispositivo e mostrá-la na próxima vez que o aplicativo é aberto, mesmo se a campanha manualmente é encerrada.
- Iniciar mais de uma campanha ao mesmo tempo pode levar mais tempo para digitalizar sua base de usuários (tente só iniciar uma campanha por vez com um máximo de quatro, também destino somente aos usuários ativos para que usuários antigos não precisam ser verificado).
- Se você usar a opção "Ignorar público, envio será enviado aos usuários por meio da API" na seção "Campanha" de uma campanha de alcance, campanha não enviará automaticamente, você precisará enviá-la manualmente por meio da API alcançar.
- Se você usar uma categoria personalizada no alcance para exibir as notificações do aplicativo, você precisará seguir o ciclo de vida correto de uma notificação ou então a notificação não pode ser limpos quando o usuário descartá-la.

 
