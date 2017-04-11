<properties
    pageTitle="Azure AD Connect: Portas | Microsoft Azure"
    description="Esta é uma página de referência técnica para portas que precisam estar aberto para Azure AD Connect"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Identidade híbrido obrigatório portas e protocolos
O documento a seguir é uma referência técnica para fornecer informações sobre as portas necessárias e os protocolos necessários para implementar uma solução de identidade híbrido. Use a ilustração a seguir e consulte a tabela correspondente.

![O que é Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - Azure AD se conectar e AD local
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor do Azure AD Connect e AD local.

Protocolo | Portas | Descrição
--------- | --------- |---------
DNS|53 TCP/UDP)| Pesquisas DNS na floresta de destino.
Kerberos|88 TCP/UDP)| Autenticação de Kerberos à floresta AD.
MS-RPC |135 TCP/UDP)| Usado durante a configuração inicial do assistente Azure AD Connect quando se vincula à floresta AD.
LDAP|389 TCP/UDP)| Usado para importar dados do AD. Dados são criptografados com a entrada de Kerberos & selo.
LDAP/SSL|636 TCP/UDP)| Usado para importar dados do AD. A transferência de dados é assinada e criptografada. Usado somente se você estiver usando SSL.
RPC |49152 a 65535 (aleatória alta RPC Port)(TCP/UDP)| Usado durante a configuração inicial do Azure AD Connect quando se vincula florestas AD. Para obter mais informações, consulte [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) .

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - Azure AD conectar e Azure AD
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor do Azure AD Connect e o Azure AD.

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usado para baixar CRLs (listas de certificados revogados) para verificar os certificados SSL.
HTTPS|443(TCP/UDP)| Usado para sincronizar com o Azure AD.

Para obter uma lista de IP e URLs endereços você precisa abrir no firewall, consulte [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tabela 3 - Azure AD conectar e servidores de Federação/WAP
Esta tabela descreve as portas e protocolos necessários para a comunicação entre o servidor do Azure AD Connect e os servidores de Federação/WAP.  

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usado para baixar CRLs (listas de certificados revogados) para verificar os certificados SSL.
HTTPS|443(TCP/UDP)| Usado para sincronizar com o Azure AD.
WinRM|5985| Ouvinte de WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - WAP e servidores de Federação
Esta tabela descreve as portas e protocolos necessários para a comunicação entre os servidores de Federação e WAP.

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Usado para autenticação.

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e usuários
Esta tabela descreve as portas e protocolos necessários para a comunicação entre os usuários e os servidores WAP.

Protocolo |Portas |Descrição
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Usado para autenticação do dispositivo.
TCP|49443 (TCP)| Usado para autenticação de certificado.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 6a & 6b - agente de integridade de conectar-se do Azure AD para AD FS/Sync () e Azure AD
As tabelas a seguir descrevem os pontos de extremidade, portas e protocolos necessários para comunicação entre agentes de integridade de conectar-se do Azure AD e Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 6a - portas e protocolos para agente de integridade de conectar-se do Azure AD para AD FS/Sync () e Azure AD
Esta tabela descreve as seguintes portas de saída e os protocolos necessários para a comunicação entre os agentes de integridade de conectar-se do Azure AD e Azure AD.  

Protocolo |Portas  |Descrição
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Saída
Barramento de serviço Azure|5671 TCP/UDP)| Saída

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6B - pontos de extremidade do agente de integridade de conectar-se do Azure AD para AD FS/Sync () e Azure AD
Para obter uma lista de pontos de extremidade, consulte [a seção requisitos para o agente de integridade de conectar-se do Azure AD](active-directory-aadconnect-health-agent-install.md#requirements).
