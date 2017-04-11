<properties
    pageTitle="Lista de compatibilidade de Federação do Azure AD"
    description="Esta página tem provedores de identidade não sejam da Microsoft que podem ser usadas para implementar o logon único."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidade de Federação do Azure AD
Active Directory do Azure fornece logon único em e aprimorado segurança de acesso do aplicativo para o Office 365 e outros serviços Online da Microsoft para híbrido e implementações somente na nuvem sem a necessidade de qualquer solução não sejam da Microsoft. O Office 365, como a maioria dos Serviços Online da Microsoft, é integrado com o Active Directory do Azure para autorização, autenticação e serviços de diretório. Active Directory do Azure também fornece logon único para milhares de aplicativos SaaS e aplicativos da web no local. Confira a Galeria de aplicativo do Active Directory do Azure para aplicativos SaaS suportados.

Para organizações que têm investido em soluções de Federação não sejam da Microsoft, este tópico contém orientações para configurar o logon único para seus usuários do Active Directory do Windows Server com os Serviços Online da Microsoft usando provedores de identidade não sejam da Microsoft na "Azure Active Directory federation compatibilidade lista" abaixo. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Grupo de computador Oxford](http://oxfordcomputergroup.com/), um terceiro, em nome da Microsoft, testado essas experiências de logon única usando provedores de identidade não sejam da Microsoft em relação a um conjunto de casos de uso comuns com o Active Directory do Azure.

Para obter informações sobre como você pode fazer o seu provedor de identidade de terceiros listado aqui, entre em contato com o grupo de computador Oxford em [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Grupo de computador Oxford testado apenas a funcionalidade de Federação desses cenários de logon único. Grupo de computador Oxford não executou qualquer teste de sincronização, autenticação de dois fatores, componentes etc desses cenários de logon único.

>Uso de entrar pela identificação alternativo ao UPN também não testado nesse programa.



- [Active Directory do Azure](#azure-active-directory)
- [Serviços de Federação do servidor de identidade Virtual IDM ideal](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [PingFederate 7.2](#pingfederate-72) 
- [PingFederate 8. x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM Tivoli federado Gerenciador de identidades 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Gerenciador de acesso NetIQ 4.0.1](#netiq-access-manager-401) 
- [IP grande com o Gerenciador de política de acesso IP grande versão 11.3 x – 11,6 x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portal de espaço de trabalho de VMware versão 2.1](#vmware-workspace-portal-version-21) 
- [Assinar & Ir 5.3](#signampgo-53) 
- [IceWall federação versão 3.0](#icewall-federation-version-30) 
- [Nuvem de seguro de autoridade de certificação](#ca-secure-cloud) 
- [Dell uma identidade nuvem Access Manager v 7.1](#dell-one-identity-cloud-access-manager-v71) 
- [Logon único AuthAnvil 4,5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Desde que esses são os produtos de terceiros, Microsoft não oferece suporte para a implantação, configuração, solução de problemas, melhores práticas, etc. problemas e perguntas sobre esses provedores de identidade. Para suporte e perguntas sobre esses provedores de identidade, contate os terceiros com suporte diretamente.

>Esses provedores de identidade de terceiros foram testadas para interoperabilidade com serviços de nuvem da Microsoft usando o Web Services Federation e WS-Trust protocolos somente. Teste não incluiu usando o protocolo SAML.

## <a name="azure-active-directory"></a>Active Directory do Azure 
Active Directory do Azure pode autenticar usuários por federar com seu local Active Directory ou sem um servidor de federação local por meio do uso sincronização de senha. 

A seguir é a matriz de suporte de cenário para esta experiência de logon: 


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|
|Aplicativos modernos usando ADAL como o Office 2016| Com suporte|Nenhum|

Para obter mais informações sobre como usar o Active Directory do Azure com o AD FS, consulte [Serviços de Federação do Active Directory (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Para obter mais informações sobre como usar o Active Directory do Azure com a sincronização de senha consulte [Azure AD Connect](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Serviços de Federação do servidor de identidade Virtual IDM ideal 
Ideal IDM Virtual identidade servidor Serviços de Federação pode autenticar os usuários que residem em locais Active diretórios clientes.

Este é o cenário esta experiência de logon única de matriz de suporte:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Para obter mais informações sobre o acesso para cliente políticas consulte [limitando o acesso ao Office 365 serviços baseado no local do cliente.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11 implementa o padrão de identidade de federação de WS amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum (versões anteriores atualize 6.11|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter instruções sobre como configurar isso a PingFederate STS fornecer a experiência de logon única para os usuários do Active Directory, baixe o pdf [aqui.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2 
PingFederate 7.2 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para as instruções sobre como configurar esse STS PingFederate fornecer a experiência de logon única para os usuários do Active Directory, consulte [aqui.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8. x 
PingFederate 8. x implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para as instruções sobre como configurar esse STS PingFederate fornecer a experiência de logon única para os usuários do Active Directory, consulte [aqui.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify ajuda a fornecer uma federados única experiência de logon do Office 365 sem a necessidade de um servidor de federação local de hospedagem.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Não há suporte para o controle de acesso do cliente 

Para obter mais informações sobre Centrify, consulte [aqui.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli federado Gerenciador de identidades 6.2.2 
IBM Tivoli federado identidade Manager 6.2.2 com IBM segurança Access Manager para Microsoft aplicativos 1,4 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre o IBM Tivoli federado identidade Manager, consulte [IBM Gerenciador de acesso de segurança para Microsoft Applications.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para proporcionar uma experiência de logon único e estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para saber mais sobre SecureAuth, consulte [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 cumulativo lançamento 4
CA SiteMinder federação 12.52 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre CA SiteMinder, consulte [CA SiteMinder federação.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
RadiantOne nuvem federação serviço CFS () 3.0 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para saber mais sobre RadiantOne CFS, consulte [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Autenticação integrada do Windows requer a instalação do servidor de web adicionais e aplicativo de Okta.|
| Aplicativos de cliente avançados como Lync, a assinatura do Office e CRM |  Com suporte |Autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para saber mais sobre Okta, consulte [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin como testado em maio de 2014 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Autenticação integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para saber mais sobre OneLogin, consulte [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Gerenciador de acesso NetIQ 4.0.1 
Gerenciador de acesso NetIQ 4.0.1 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |* Kerberos contratos com suporte|
| Aplicativos de cliente rich como o Lync, assinatura do Office, CRM |  Com suporte |Não há suporte para a autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

* NetIQ suporte à autenticação de Kerberos por meio da configuração de um contrato de Kerberos.  Para obter assistência com essa configuração, contate NetIQ ou exibir o guia de instalação. Para obter mais informações sobre o Gerenciador de acesso de NetIQ, consulte [Gerenciador de acesso NetIQ.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>IP grande com ver IP grande Gerenciador de política de acesso. 11.3 x – 11,6 x 
O IP de grande com o Gerenciador de política de acesso, (APM) ver IP grande. x 11.3 – 11,6 x implementa o padrão de identidade SAML amplamente usado para proporcionar uma experiência de logon único e estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Sem suporte |Sem suporte|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre o Gerenciador de política de acesso de IP grande, consulte [Gerenciador de política de acesso de IP grande.](https://f5.com/products/modules/access-policy-manager) 

Para as Gerenciador de política de acesso de IP grande obter instruções sobre como configurar esse STS para fornecer a experiência de logon única para o Active Directory Users, baixe o pdf [aqui.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portal de espaço de trabalho de VMware versão 2.1 
Portal de espaço de trabalho de VMware versão 2.1 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre o Portal de espaço de trabalho de VMware versão 2.1, baixe o pdf [aqui.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Assinar & Ir 5.3 
Assinar & Ir 5.3 implementa a identidade WS federação, WS-Trust amplamente usado padrão para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Contratos de Kerberos suportados |
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM | Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|


Entrada & Ir 5.3 dá suporte à autenticação Kerberos por meio de configuração de um contrato de Kerberos.  Para obter assistência com essa configuração, entre em contato com Ilex ou exibir o guia de instalação [aqui.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall federação versão 3.0 
IceWall federação versão 3.0 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para saber mais sobre a federação de IceWall, veja [aqui](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) e [aqui.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>Nuvem de seguro de autoridade de certificação 

Nuvem de seguro de CA implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre a nuvem de seguro de autoridade de certificação, consulte [CA seguro nuvem.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell uma identidade nuvem Access Manager v 7.1 
Gerenciador de acesso de nuvem do Dell uma identidade implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Nenhum|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM |  Com suporte |Nenhum|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|

Para obter mais informações sobre Dell uma identidade nuvem Gerenciador de acesso, consulte [Dell Gerenciador de acesso de nuvem uma identidade.](http://software.dell.com/products/cloud-access-manager)

 Para obter as instruções sobre como configurar esse STS fornecer a experiência de logon única para os usuários do Office 365, consulte [Configurar usuários do Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>Logon único AuthAnvil 4,5 
AuthAnvil única entrada na 4,5 implementa o padrão de identidade de WS federação, WS-Trust amplamente usado para fornecer um logon único e a estrutura do exchange de atributo.

A matriz de suporte de cenário para esta experiência de logon única é o seguinte:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Clientes baseados na Web, como o Exchange Web Access e o SharePoint Online | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Aplicativos de cliente avançados como o Lync, a assinatura do Office e CRM | Com suporte |Não há suporte para a autenticação integrada do Windows|
| Clientes de email-rich como o Outlook e ActiveSync |  Com suporte |Nenhum|


Para obter mais informações, consulte [AuthAnvil logon único.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
