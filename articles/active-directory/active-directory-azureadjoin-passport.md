<properties
    pageTitle="Autenticação de identidades sem senhas por meio do Microsoft Passport | Microsoft Azure"
    description="Fornece uma visão geral do Microsoft Passport e informações adicionais sobre como implantar o Microsoft Passport."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Autenticação de identidades sem senhas por meio do Microsoft Passport

Os métodos de autenticação com senhas sozinhas atuais não são suficientes para manter os usuários seguros. Os usuários reutilizar e esquecer senhas. As senhas diferenciam breachable, phishable, estando propensa a perca e podem ser adivinhada. Elas também obtêm difícil se lembrar e sujeitos a ataques como "[passar o hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-microsoft-passport"></a>Sobre o Microsoft Passport
Microsoft Passport é uma chave privada/pública ou abordagem de autenticação baseada em certificado para organizações e consumidores que ultrapassa senhas. Essa forma de autenticação depende de credenciais de par de chaves que podem substituir senhas e são resistentes a violações, thefts e phishing.

 Microsoft Passport permite ao usuário autenticar para uma conta da Microsoft, uma conta do Active Directory do Windows Server, uma conta do Microsoft Azure Active Directory (AD Azure) ou um serviço não sejam da Microsoft que oferece suporte à autenticação de Fast identidade Online (FIDO). Após uma verificação em duas etapas iniciais durante a inscrição do Microsoft Passport, Microsoft Passport está configurado no dispositivo do usuário e o usuário define um gesto, que pode ser Windows Hello ou um PIN. O usuário fornece o gesto para verificar sua identidade. Windows usa o Microsoft Passport para autenticar o usuário e ajudá-lo para acessar recursos protegidos e serviços.

A chave privada é disponibilizada somente por meio de um "gesto do usuário" como um PIN, biométrica ou um dispositivo remoto como um cartão inteligente que o usuário usa para entrar no dispositivo. Esta informação está vinculada a um certificado ou um par de chaves assimétrica. A chave privada é attested se o dispositivo tiver um chip (Trusted plataforma módulo) de hardware. A chave privada nunca sai do dispositivo.

A chave pública é registrada com o Active Directory do Azure e o Windows Server Active Directory (para locais). Provedores de identidade (IDPs) validar o usuário mapeando a chave pública do usuário para a chave privada e fornecem informações de entrada por meio de uma vez senha (OTP), PhoneFactor ou um mecanismo de notificação diferente.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Por que as empresas devem adotar Microsoft Passport

Ativando o Microsoft Passport, corporativo pode fazer seus recursos ainda mais seguro por:

* Configurando Microsoft Passport com a opção de hardware preferencial. Isso significa que chaves serão geradas em TPM 1.2 ou TPM 2.0 quando disponível. Quando o TPM não estiver disponível, o software irá gerar a chave.

* Definindo a complexidade e o tamanho do PIN, e se o uso de saudação está habilitado em sua organização.

* Configurando o Microsoft Passport para dar suporte a cartão inteligente semelhante cenários usando confiança baseada em certificados.

## <a name="how-microsoft-passport-works"></a>Como funciona o Microsoft Passport
1. Teclas são geradas no hardware por TPM ou software. Muitos dispositivos possuem um chip TPM interno que protege o hardware por integrar chaves de criptografia em dispositivos. TPM 1.2 ou TPM 2.0 gera chaves ou certificados que são criados das chaves geradas.

2. O TPM atesta estas teclas de hardware ligadas.

3. Um gesto de desbloquear único desbloqueie o dispositivo. Esse gesto permite acesso a vários recursos se o dispositivo é Azure ou domínio AD-unidas.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Como funciona o ciclo de vida do Microsoft Passport

![Ciclo de vida do Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

O diagrama anterior ilustra o par de chaves pública/particular e a validação pelo provedor de identidade. Cada uma dessas etapas é explicado em detalhes aqui:

1. O usuário prova sua identidade por meio de vários métodos internos de revisão de texto (gestos, cartões inteligentes físicas, autenticação multifator) e envia essas informações para uma identidade provedor (IDP) como o Azure Active Directory ou do Active Directory local.

2. O dispositivo depois cria a chave, atesta a chave, leva a parte dessa chave pública, anexa com instruções de estação, sinais em e envia para IDP para registrar a chave.

4. Assim que o IDP registra a parte da chave pública, IDP desafios o dispositivo para entrar com a parte da chave particular.

5. Em seguida, IDP valida e emite o token de autenticação que permite o acesso do usuário e o dispositivo os recursos protegidos. IDPs pode escrever aplicativos de plataforma cruzada ou use o suporte ao navegador (via APIs JavaScript/Webcrypto) para criar e usar credenciais do Microsoft Passport para seus usuários.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Os requisitos de implantação do Microsoft Passport
### <a name="at-the-enterprise-level"></a>No nível empresarial

* A empresa tem uma assinatura do Azure.

### <a name="at-the-user-level"></a>No nível de usuário

* O computador do usuário executa o Windows 10 Professional ou Enterprise.

Para obter instruções detalhadas de implantação, consulte [Habilitar o Microsoft Passport para trabalhar na organização](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Informações adicionais

* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
