<properties
    pageTitle="Cenários avançados com Azure autenticação multifator e 3º VPNs de festa"
    description="Esta página fornece informações sobre configuração de instalação passo a passo para MFA Azure com produtos de terceiros 3º secundários."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Cenários avançados com Azure autenticação multifator e 3º VPN de terceiros
Azure autenticação multifator pode ser usada para conectar-se perfeitamente com uma variedade de soluções VPN festa 3º.  Isso inclui aparelho Cisco® ASA VPN, aparelho Citrix NetScaler SSL VPN e o aparelho de redes Secure Access/Pulse seguro conectar seguro SSL VPN Juniper.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Aparelho de ASA VPN Cisco e autenticação multifator do Azure
Azure autenticação multifator integra diretamente em seu aparelho de Cisco® ASA VPN para fornecer segurança adicional para logon AnyConnect Cisco® VPN e acesso ao portal.  Isso pode ser feito usando o LDAP RADIUS protocolo ou.  Selecione um dos seguintes procedimentos para baixar os guias de configuração passo a passo detalhadas.

Guia de configuração  | Descrição
------------- | ------------- |
[Cisco ASA com configuração de MFA VPN AnyConnect da e Azure para LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integram perfeitamente seu aparelho Cisco ASA VPN a MFA Azure usando LDAP|
[Cisco ASA com configuração de MFA VPN AnyConnect da e Azure para RAIO](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integram perfeitamente seu aparelho Cisco ASA VPN a MFA Azure usando o RADIUS

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN e Azure autenticação multifator
Azure autenticação multifator integra diretamente em seu aparelho de Citrix NetScaler SSL VPN para fornecer segurança adicional para logon Citrix NetScaler SSL VPN e acesso ao portal.  Isso pode ser feito usando o LDAP RADIUS protocolo ou.  Selecione um dos seguintes procedimentos para baixar os guias de configuração passo a passo detalhadas.

Guia de configuração  | Descrição
------------- | ------------- |
[Configuração de MFA Citrix NetScaler SSL VPN e Azure para LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar perfeitamente sua Citrix NetScaler SSL VPN com o equipamento de MFA Azure usando LDAP|
[Configuração de MFA Citrix NetScaler SSL VPN e Azure para RAIO](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integram perfeitamente seu aparelho Citrix NetScaler SSL VPN a MFA Azure usando o RADIUS

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Dispositivo de Juniper/Pulse seguro SSL VPN e autenticação multifator do Azure
Azure autenticação multifator integra diretamente em seu aparelho de Juniper/Pulse seguro SSL VPN para fornecer segurança adicional para logon Juniper/Pulse seguro SSL VPN e acesso ao portal.  Isso pode ser feito usando o LDAP RADIUS protocolo ou.  Selecione um dos seguintes procedimentos para baixar os guias de configuração passo a passo detalhadas.

Guia de configuração  | Descrição
------------- | ------------- |
[Configuração de MFA Juniper/Pulse seguro SSL VPN e Azure para LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Integrar perfeitamente seu Juniper/Pulse seguro SSL VPN com o equipamento de MFA Azure usando LDAP|
[Configuração de MFA Juniper/Pulse seguro SSL VPN e Azure para RAIO](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integram perfeitamente seu aparelho Juniper/Pulse seguro SSL VPN a MFA Azure usando o RADIUS
