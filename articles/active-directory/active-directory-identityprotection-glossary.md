<properties
    pageTitle="Glossário de proteção de identidade do Active Directory do Azure | Microsoft Azure"
    description="Glossário de proteção de identidade do Active Directory do Azure"
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, descoberta de aplicativo de nuvem, gerenciando aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança, Glossário"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de proteção de identidade do Active Directory do Azure 


### <a name="at-risk-user"></a>Risco (usuário)  
Um usuário com um ou mais eventos de risco ativo. 

### <a name="atypical-sign-in-location"></a>Local de entrada atípico   
Uma entrada de uma localização geográfica que não seja típica para o usuário específico, usuários semelhantes ou locatário.

### <a name="azure-ad-identity-protection"></a>Proteção de identidade do Azure AD    
Um módulo de segurança do Active Directory do Azure que fornece uma visão consolidada em eventos de risco e possíveis vulnerabilidades afetando identidades de uma organização.

### <a name="conditional-access"></a>Acesso condicional  
Uma política para proteger o acesso aos recursos. Regras de acesso condicional são armazenadas no Active Directory do Azure e são avaliadas por Azure AD antes de conceder acesso ao recurso.  Exemplo de regras inclui restringindo o acesso com base no local do usuário, método de autenticação de usuário ou de integridade do dispositivo.

### <a name="credentials"></a>Credenciais     
Informações que incluem identificação e prova de identificação é usada para obter acesso a locais e recursos de rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento   
Um registro de uma atividade do Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falsos positivos (evento de risco) 
Um status de evento de risco definir manualmente por um usuário de proteção de identidade, indicando que o evento de risco foi investigação e incorretamente foi sinalizado como um evento de risco.

### <a name="identity"></a>Identidade    
Uma pessoa ou entidade que deve ser verificada por meio da autenticação, com base em critérios como senha ou um certificado.

### <a name="identity-risk-event"></a>Evento de risco de identidade     
Evento AAD que foi sinalizado como anômalos pela proteção de identidade e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)    
Um status de evento de risco definir manualmente por um usuário de proteção de identidade, indicando que o evento de risco é fechado sem executar uma ação de correção.

### <a name="impossible-travel-from-atypical-locations"></a>Impossível viagem de atípicos locais   
Um evento de risco disparado quando dois suplementos de entrada para o mesmo usuário são detectados, onde pelo menos um deles é de um local de entrada atípico e onde o tempo entre os suplementos de entrada é menor que o tempo mínimo levaria física deslocar entre esses locais.  

###<a name="investigation"></a>Investigação    
O processo de revisão as atividades, logs e outras informações relevantes relacionados a um evento de risco para decidir se as etapas de solução ou de minimização são necessárias, entender se e como a identidade foi comprometida e entender como a identidade comprometida foi usada.

### <a name="leaked-credentials"></a>Credenciais perdidas  

Um evento de risco disparado quando credenciais do usuário atual (nome de usuário e senha) são encontradas lançado publicamente na web escura por nossos pesquisadores.

### <a name="mitigation"></a>Atenuação  
Uma ação a limite ou elimine a capacidade de um ataque de explorar uma identidade comprometida ou dispositivo sem restaurar a identidade ou dispositivo para um estado seguro. Uma atenuação não resolver anterior eventos de riscos associados a identidade ou dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator 
Um método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir algo que o usuário tiver, certificado; algo conhecimento do usuário, como nomes de usuário, senhas ou frases; atributos físicos, como uma impressão digital; e atributos de pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Detecção offline   
A detecção de anomalias e avaliação de risco de um evento como tentativa de entrada após o fato, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição de política    
Uma parte de uma política de segurança que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivos, estados de dispositivo, intervalos de IP, tipos de cliente) incluídos na política ou excluídos dele.

### <a name="policy-rule"></a>Regra de política     
A parte de uma política de segurança que descreve as circunstâncias que irá disparar a política e as ações tomadas quando a política é disparada.

### <a name="prevention"></a>Prevenção  
Uma ação para evitar danos à organização por meio do abuse uma identidade ou dispositivo suspeita ou saibam que devem ser comprometida. Uma ação de prevenção não proteger o dispositivo ou identidade e não resolve eventos de risco anteriores.

### <a name="privileged-user"></a>Privilegiado (usuário)
Um usuário que no momento de um evento de risco, tinha permissões de administrador permanente ou temporária para uma ou mais recursos no Active Directory do Azure, como um Administrador Global, administrador de cobrança, administrador do serviço, administrador de usuário e senha de administrador. 

###<a name="real-time"></a>Em tempo real    
Consulte detecção em tempo real.

###<a name="real-time-detection"></a>Detecção em tempo real  
A detecção de anomalias e avaliação de risco de um evento como tentativa de entrada antes do evento são permitidas para continuar.

### <a name="remediated-risk-event"></a>Remediadas (evento de risco)     
Um status de evento de risco definido automaticamente pelo proteção de identidade, indicando que o evento de risco foi remediado usando a ação de correção padrão para esse tipo de evento de risco. Por exemplo, quando a senha do usuário é redefinida, muitos eventos de risco que indicam que a senha anterior foi comprometida são automaticamente remediados.

### <a name="remediation"></a>Correção     
Uma ação a uma identidade ou um dispositivo que estava anteriormente suspeita ou comprometida seguro. Uma ação de correção restaura a identidade ou dispositivo para um estado seguro e gerar eventos de risco anteriores associados a identidade ou dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)   
Um status de evento de risco definir manualmente por um usuário de proteção de identidade, indicando que o usuário fez uma ação de correção apropriado fora da proteção de identidade e que o evento de risco deve ser considerado fechada.

###<a name="risk-event-status"></a>Status de evento de risco    
Uma propriedade de um evento de risco, que indica se o evento está ativo e se fechada, o motivo para fechá-lo.

###<a name="risk-event-type"></a>Tipo de evento de risco  
Uma categoria para o evento de risco, indicando o tipo de anomalia que causou o evento deve ser considerado arriscada.

###<a name="risk-level-risk-event"></a>Nível de risco (evento de risco)  
Uma indicação (alta, média ou baixa) da gravidade do evento de risco para ajudar os usuários de proteção de identidade a priorizar as ações que elas tomar para reduzir o risco à sua empresa. 

###<a name="risk-level-sign-in"></a>Nível de risco (entrar) 
Uma indicação (alta, média ou baixa) da probabilidade de que para um entrada-in específico, alguém está tentando usar a identidade do usuário.

###<a name="risk-level-user-compromise"></a>Nível de risco (compromisso do usuário) 
Uma indicação (alta, média ou baixa) da probabilidade de que uma identidade foi comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)  
Uma indicação (alta, média ou baixa) da gravidade da vulnerabilidade para ajudar os usuários de proteção de identidade a priorizar as ações que elas tomar para reduzir o risco à sua empresa.

### <a name="secure-identity"></a>Proteger (identidade)   
Correção de agir como uma alteração de senha ou fazer uma nova imagem para restaurar uma identidade potencialmente comprometida para um estado toda prova de máquina.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras de política e condição. Uma política pode ser aplicada a entidades como usuários, grupos, aplicativos, dispositivos, plataformas de dispositivos, estados de dispositivo, intervalos de IP e tipos de cliente Auth2.0. Quando uma política está habilitada, ele será avaliado sempre que uma entidade incluída na política é emitida um token de um recurso.

### <a name="sign-in-v"></a>Entrar (v) 
Para autenticar uma identidade no Active Directory do Azure.

### <a name="sign-in-n"></a>Entrar (n) 
O processo ou ação de autenticar uma identidade no Active Directory do Azure e o evento que captura essa operação.

###<a name="sign-in-from-anonymous-ip-address"></a>Entrar do endereço IP anônimo    
Um evento de risco acionada após um bem-sucedido entrada de endereço IP que foi identificado como um endereço IP de proxy anônimo.

###<a name="sign-in-from-infected-device"></a>Entrar do dispositivo infectado 
Um evento de risco disparado quando um entrar se originam de um endereço IP que é conhecido a ser usado por um ou mais dispositivos comprometidos, que estão ativamente tentando se comunicar com um servidor de bot.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Entrar do endereço IP com atividades suspeitas 
Um evento de risco acionada após um bem-sucedido entrar de um IP de endereços com um grande número de tentativas de login falhas em várias contas de usuário em um período curto de tempo.

###<a name="sign-in-from-unfamiliar-location"></a>Entrar no local familiarizado 
Um evento de risco disparado quando um usuário entra com êxito de um novo local (IP, Latitude/Longitude e ASN).

###<a name="sign-in-risk"></a>Entrar risco     
Consulte risco nível (entrar)

###<a name="sign-in-risk-policy"></a>Política de risco entrar  
Uma política de acesso condicional que avalia o risco a um sinal-in específico e aplica reduções com base em regras e condições predefinidas.

###<a name="user-compromise-risk"></a>Risco de compromisso do usuário     
Consulte risco nível (compromisso do usuário)

###<a name="user-risk"></a>Risco de usuário    
Consulte risco nível (compromisso de usuário).

###<a name="user-risk-policy"></a>Política de risco do usuário     
Uma política de acesso condicional que considera a entrar e aplica reduções com base em regras e condições predefinidas.

###<a name="users-flagged-for-risk"></a>Usuários sinalizados para riscos   
Usuários que têm eventos de risco que são ativo ou remediadas

###<a name="vulnerability"></a>Vulnerabilidade    
Uma configuração ou condição no Active Directory do Azure que torna o diretório sujeita a explorações ou ameaças.


## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md)
