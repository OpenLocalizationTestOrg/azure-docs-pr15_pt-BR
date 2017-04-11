<properties 
   pageTitle="Azure contrato móvel guia - serviço de solução de problemas" 
   description="Guias para o Azure contrato móvel de solução de problemas" 
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

# <a name="troubleshooting-guide-for-service-issues"></a>Guia de solução de problemas para problemas de serviço

A seguir são possíveis problemas que você pode encontrar com como o Azure Mobile contrato é executado.

## <a name="service-outages"></a>Interrupções de serviço

### <a name="issue"></a>Problema
- Problemas que parecem ser causado por interrupções do Azure Mobile contrato de serviço.

### <a name="causes"></a>Causas
- Problemas que parecem ser causado por interrupções de serviço do Azure Mobile contrato podem ser causados por vários problemas diferentes:
    - Isolados de problemas que originalmente aparecem sistemático a todos do Azure Mobile envolvimento
    - Problemas conhecidos causados por interrupções de servidor (nem sempre mostra no status do servidor):
    - Agendamento atrasos, erros de direcionamento, problemas de atualização do selo, parada de estatísticas coletando, paradas de envio trabalhando, interromper trabalho da API, novos aplicativos ou usuários não é possível criar, erros de DNS e erros de tempo limite na interface do usuário, API ou aplicativos em um dispositivo.
    - Nuvem dependência interrupções [Status do serviço do Azure](http://status.azure.com/)
    - Notificações por push interrupções de dependência de serviços (PNS) de notificação
    - Interrupções de App Store

1) Para testar para ver se o problema é sistemático testar a mesma função de um diferente
   
   - Aplicativo do Azure contrato Mobile integrado
   - Teste do dispositivo
   - Versão do SO do dispositivo de teste
   - Campanha
   - Conta de usuário de administrador
   - Navegador (IE, Firefox, Chrome, etc.)
   - Computador

2) Para testar se o problema afeta apenas a interface do usuário ou a API:

   - Teste a mesma função da interface do usuário do Azure Mobile contrato e da API do Azure Mobile contrato.

3) Para testar se o problema é com a rede de telefone celular:

   - Teste enquanto estiver conectado à Internet via WIFI e enquanto conectada por meio de sua rede de telefone celular 3G.
   - Confirme se o firewall não está bloqueando qualquer um dos endereços IP do Azure Mobile contrato ou portas.

4) Para testar se o problema é com o seu dispositivo:

   - Teste se seu dispositivo é capaz de se conectar ao Azure Mobile contrato com outro aplicativo integrado do Azure Mobile contrato.
   - Teste que você pode gerar eventos, trabalhos e travamentos de seu telefone que pode ser visto na interface do usuário do Azure Mobile contrato. 
   - Testar se você pode enviar notificações por push da interface do usuário do Azure Mobile contrato para seu dispositivo com base em sua identificação do dispositivo. 

5) Para testar se o problema for com seu aplicativo:

   - Instalar e testar seu aplicativo de um emulador em vez de em um dispositivo físico:
   
6) Para testar se o problema for com atualizações de sistema operacional para o usuário final dispositivos, que requer uma atualização SDK para resolver:

   - Teste seu aplicativo em diferentes dispositivos com diferentes versões do sistema operacional.
   - Confirme que você está usando a versão mais recente do SDK.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Conectividade e problemas de informações incorretas

### <a name="issue"></a>Problema
- Problemas de registro em log para a interface do usuário do Azure Mobile contrato.
- Erros de Conexão com o Azure Mobile contrato API.
- Problemas ao carregar marcas de informações de aplicativo por meio da API do dispositivo.
- Problemas de download logs ou dados exportados do Azure Mobile contrato.
- Informações incorretas mostradas na interface do usuário do Azure Mobile contrato.
- Informações incorretas mostradas em logs do Azure Mobile contrato.

### <a name="causes"></a>Causas
* Confirme que sua conta de usuário tem permissões suficientes para executar a tarefa.
* Confirme se o problema não está isolado para um computador ou rede local.
* Confirme que se o serviço do Azure Mobile contrato tem não informada interrupções.
* Confirme que seus arquivos de marca de informações de aplicativo sigam todas estas regras:
    - Use somente o conjunto de caracteres UTF8 (não há suporte para o conjunto de caracteres ANSI).
    - Usar uma vírgula "," como o caractere separador (você pode abrir um serviço de solicitação para solicitação para alterar o caractere separador de arquivo. csv de uma vírgula "," para um outro caractere como ponto e vírgula ";").
    - Use todas as letras minúsculas para valores booleanos "true" e "false".
    - Use um arquivo que for menor do que o tamanho máximo de arquivo de 35MB.
 
