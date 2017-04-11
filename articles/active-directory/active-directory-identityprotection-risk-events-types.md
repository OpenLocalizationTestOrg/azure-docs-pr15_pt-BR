<properties
    pageTitle="Tipos de eventos de risco detectados pelo Azure Active Directory proteção de identidade | Microsoft Azure"
    description="Este tópico apresenta uma visão geral detalhada dos tipos disponíveis de eventos de risco no Azure Active Directory proteção de identidade"
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="markvi"/>

#<a name="types-of-risk-events-detected-by-azure-active-directory-identity-protection"></a>Tipos de eventos de risco detectados pelo Azure Active Directory proteção de identidade 

No Azure Active Directory identidade proteção, eventos de risco são eventos que:

- foram sinalizado como suspeito

- indica que uma identidade pode ter sido comprometida. 

Este tópico fornece uma visão geral detalhada dos tipos disponíveis de eventos de risco.


## <a name="leaked-credentials"></a>Credenciais perdidas

Credenciais perdidas forem encontradas lançado publicamente na web escura por pesquisadores de segurança da Microsoft. Essas credenciais são geralmente encontradas em texto sem formatação. Eles são comparados credenciais do Azure AD e se houver uma correspondência, eles são relatados como "Leaked credencial" em proteção de identidade.

Perdido credenciais eventos de risco são classificados como um evento de risco gravidade "Alta", pois fornecem uma indicação clara de que o nome de usuário e senha estão disponíveis para um usuário mal-intencionado.

## <a name="impossible-travel-to-atypical-locations"></a>Viagem impossível para locais atípicos

Este tipo de evento de risco identifica dois sinal-ins provenientes locais geograficamente distantes, onde pelo menos um dos locais também pode estar atípico do usuário, fornecidas ao comportamento anterior. Além disso, o tempo entre os dois sinal-ins é menor que o tempo que teria levado o usuário passe da primeiro local para a segunda, indicando que um usuário diferente está usando as mesmas credenciais. 

Este algoritmo de aprendizado de máquina que ignora óbvio "*falsos positivos*" contribuir para a condição de viagem impossível, como VPNs e locais regularmente usados por outros usuários na organização.  O sistema tem um período inicial de aprendizagem de 14 dias durante os quais ele aprende uma nova entrada comportamento de usuário.

Viagem impossível geralmente é um bom indicador que um hacker conseguiu com êxito entrar. No entanto, falsos positivos podem ocorrer quando um usuário está viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos é aplicativos que passam incorretamente servidor IPs como cliente IPs, que pode dar a aparência de entrada-ins acontecendo do data center onde o aplicativo do back-end está hospedado (geralmente esses são Microsoft dos data centers, que podem dar a aparência de entrada-ins demorando colocar da Microsoft pertencente endereços IP). Como resultado dessas falsos positivos, o nível de risco para este evento de risco é "**Médio**".

##<a name="sign-ins-from-infected-devices"></a>Suplementos de entrada de dispositivos infectados

Este tipo de evento de risco identifica suplementos de entrada de dispositivos infectados com malware, que são conhecidos ativamente se comunicar com um servidor de bot. Isso é determinado por endereços IP do dispositivo do usuário contra endereços IP que estava conectado com um servidor de bot de correlação. 

Este evento de risco identifica endereços IP, não os dispositivos de usuário. Se vários dispositivos atrás de um único endereço IP e apenas alguns estão controlados por uma rede de bot, suplementos de entrada de outros dispositivos meu disparador este evento desnecessariamente, que é o motivo para classificar este evento de risco como "**baixa**".  

Recomendamos que você entre em contato com o usuário e verificar todos os dispositivos do usuário. Também é possível que dispositivo pessoal de um usuário está infectado ou conforme mencionado anteriormente, que alguém estava usando um dispositivo infectado do mesmo endereço IP como o usuário. Dispositivos infectados geralmente estão infectados por malware que ainda não foram identificados por um software antivírus e também pode indicar como hábitos de usuário incorretos que podem ter causado o dispositivo para infectados.

Para obter mais informações sobre como infecções de malware de endereço, consulte o [Centro de proteção contra Malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## <a name="sign-ins-from-anonymous-ip-addresses"></a>Suplementos de entrada de endereços IP anônimos

Este tipo de evento de risco identifica os usuários que têm conectado com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies são usados por pessoas que deseja ocultar o endereço IP do seu dispositivo e podem ser usadas intenções maliciosas.

Recomendamos que você contate imediatamente o usuário para verificar se estivessem usando anônimos endereços IP. O nível de risco para este tipo de evento de risco é "**Médio**" porque em si um IP anônimo não é uma forte indicação de um compromisso de conta.

## <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Suplementos de entrada de endereços IP com atividades suspeitas

Este tipo de evento de risco identifica os endereços IP do qual um grande número de tentativas de entrar com falha foram visto, em várias contas de usuário, em um período curto de tempo. Isso corresponde padrões de tráfego de endereços IP usados pelo ataques e é um indicador forte que contas estão já ou estão prestes a ser comprometida. Este é uma algoritmo que ignora óbvio "*falsos positivos*", como endereços IP usados regularmente por outros usuários na organização de aprendizado de máquina.  O sistema tem um período inicial de aprendizagem de 14 dias onde ele aprende o comportamento de entrada de um novo usuário e novo locatário.

Recomendamos que você entre em contato com o usuário para verificar se eles realmente conectado de um endereço IP que foi marcado como suspeito. O nível de risco para este tipo de evento é "**Médio**" porque vários dispositivos podem ser atrás o mesmo endereço IP, enquanto apenas alguns podem ser responsável pela atividade suspeita. 


## <a name="sign-in-from-unfamiliar-locations"></a>Entrar de locais desconhecidos

Este tipo de evento de risco é um mecanismo de avaliação de entrar em tempo real que considera passaram locais de entrada (IP, Latitude / Longitude e ASN) para determinar o novo / familiarizados locais. O sistema armazena informações sobre locais anteriores usado por um usuário e considera estes locais "conhecidos". O risco ainda é disparado quando a entrada do ocorre de um local que não ainda esteja na lista de locais conhecidos. O sistema tem um período inicial de aprendizagem de 14 dias, durante o qual ele não sinalizar qualquer novos locais como locais desconhecidos. O sistema também ignora suplementos de entrada de dispositivos conhecidos e locais geograficamente Fechar para um local familiar. <br>
Locais familiarizados podem fornecer uma indicação forte que um invasor é capaz de tentar usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário estiver viajando, experimentando um novo dispositivo ou usa uma VPN novo. Como resultado dessas falsos positivos, o nível de risco para este tipo de evento é "**Médio**".


## <a name="azure-ad-anomalous-activity-reports"></a>Relatórios de atividade anormal do Azure AD

Alguns desses eventos de risco foram disponíveis por meio dos relatórios de atividade anormal do Azure AD no portal do Azure. A tabela a seguir lista os vários tipos de evento de risco e o relatório de **Atividade anormal do Azure AD** correspondente. A Microsoft continua a investir neste espaço e planos para melhorar a precisão de detecção dos eventos de risco existentes e adicionar novos tipos de evento de risco continuamente continuamente. 



| Tipo de evento de risco de proteção de identidade | Correspondente Azure AD relatório de atividade anormal |
| :-- | :-- |
| Credenciais perdidas    | Usuários com credenciais perdidas |
| Viagem impossível para locais atípicos | Atividade de entrada irregular |
| Suplementos de entrada de dispositivos infectados    | Suplementos de entrada de dispositivos possivelmente infectados |
| Suplementos de entrada de endereços IP anônimos  | Suplementos de sinal de fontes desconhecidas |
| Suplementos de entrada de endereços IP com atividades suspeitas | Suplementos de entrada de endereços IP com atividades suspeitas |
| Sinais de locais desconhecidos    | - |
| Eventos de bloqueio    | - |

Os seguintes relatórios de atividade anormal do Azure AD não são incluídos como eventos de risco no Azure AD identidade proteção e, portanto, não estará disponíveis por meio de proteção de identidade. Esses relatórios ainda estão disponíveis no portal do Azure Entretanto eles serão substituídos em algum momento no futuro conforme elas estão sendo substituídas pelo eventos de risco em proteção de identidade.

- Suplementos de entrada após várias falhas
- Suplementos de entrada de várias geografia


## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md)


